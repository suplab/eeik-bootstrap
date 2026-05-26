---
applyTo: "**/*Test.java, **/*IT.java, **/*Spec.java, **/*.spec.ts, **/*Tests.java"
---

## Context

This instruction file applies to all test source files across the project. Tests are a first-class concern — untested code is unacceptable for production. This file governs test structure, naming, tooling, and quality standards across Java (JUnit 5) and Angular (Jasmine/Jest) test suites. Test code follows the same style and quality bar as production code: no magic numbers, no copy-paste, no meaningless assertions.

---

## Java Test Standards

### Framework Stack

| Tool | Role |
|------|------|
| JUnit 5 (`org.junit.jupiter`) | Test lifecycle and execution |
| AssertJ | Fluent, readable assertions |
| Mockito 5.x | Mocking and verification |
| Awaitility | Async/concurrent test assertions |
| Testcontainers | Real infrastructure in integration tests |
| Spring Boot Test Slices | `@WebMvcTest`, `@DataJpaTest`, `@JsonTest` |
| Pact / Spring Cloud Contract | Consumer-driven contract testing |

### Test Naming Convention

Use the pattern: `methodName_scenario_expectedResult`

```java
@Test
void findById_existingCustomer_returnsCustomerDto() { ... }

@Test
void findById_nonExistentId_throwsCustomerNotFoundException() { ... }

@Test
void createOrder_nullCustomerId_throwsIllegalArgumentException() { ... }

@Test
void processPayment_insufficientBalance_returnsDeclinedResult() { ... }
```

### Class Structure

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @Mock
    private PaymentGateway paymentGateway;

    @InjectMocks
    private OrderService orderService;

    @BeforeEach
    void setUp() {
        // shared test data initialization
    }

    @Test
    void createOrder_validRequest_persistsAndReturnsOrder() {
        // Arrange
        var request = OrderTestFactory.validCreateRequest();
        var savedOrder = OrderTestFactory.savedOrder();
        when(orderRepository.save(any(Order.class))).thenReturn(savedOrder);

        // Act
        OrderResponse response = orderService.createOrder(request);

        // Assert
        assertThat(response).isNotNull();
        assertThat(response.orderId()).isEqualTo(savedOrder.getId());
        assertThat(response.status()).isEqualTo(OrderStatus.PENDING);
        verify(orderRepository).save(any(Order.class));
    }
}
```

### Assertions

```java
// CORRECT: AssertJ fluent assertions
assertThat(result).isNotNull();
assertThat(result.getName()).isEqualTo("John Doe");
assertThat(result.getItems()).hasSize(3).extracting("sku").contains("SKU-001", "SKU-002");
assertThat(exception).isInstanceOf(OrderNotFoundException.class)
    .hasMessageContaining("Order not found");

// Multi-field checks: use assertSoftly to report all failures at once
assertSoftly(softly -> {
    softly.assertThat(order.getId()).isNotNull();
    softly.assertThat(order.getStatus()).isEqualTo(OrderStatus.CONFIRMED);
    softly.assertThat(order.getCustomerId()).isEqualTo(expectedCustomerId);
});

// WRONG: Never use JUnit assertEquals
assertEquals("John Doe", result.getName());  // ❌
assertTrue(result != null);                   // ❌
```

### Parameterized Tests

```java
@ParameterizedTest(name = "amount={0} should result in {1}")
@MethodSource("paymentAmountProvider")
void calculateFee_variousAmounts_returnsCorrectFee(BigDecimal amount, BigDecimal expectedFee) {
    assertThat(feeCalculator.calculate(amount)).isEqualByComparingTo(expectedFee);
}

private static Stream<Arguments> paymentAmountProvider() {
    return Stream.of(
        Arguments.of(new BigDecimal("100.00"), new BigDecimal("2.50")),
        Arguments.of(new BigDecimal("0.00"), BigDecimal.ZERO),
        Arguments.of(new BigDecimal("999.99"), new BigDecimal("24.99"))
    );
}
```

### Exception Testing

```java
@Test
void findById_notFound_throwsOrderNotFoundException() {
    when(orderRepository.findById(any(UUID.class))).thenReturn(Optional.empty());

    assertThatThrownBy(() -> orderService.findById(UUID.randomUUID()))
        .isInstanceOf(OrderNotFoundException.class)
        .hasMessageContaining("not found");
}
```

### Async Tests — Awaitility

```java
@Test
void processAsync_eventPublished_listenerReceivesEvent() {
    orderService.processAsync(orderId);

    await()
        .atMost(Duration.ofSeconds(5))
        .pollInterval(Duration.ofMillis(200))
        .untilAsserted(() ->
            assertThat(eventCaptor.getEvents()).anyMatch(e -> e.getOrderId().equals(orderId))
        );
}
```

### Spring MVC Controller Tests

```java
@WebMvcTest(OrderController.class)
class OrderControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @MockBean
    private OrderService orderService;

    @Test
    void getOrder_existingOrder_returns200WithBody() throws Exception {
        var response = OrderTestFactory.orderResponse();
        when(orderService.findById(response.orderId())).thenReturn(response);

        mockMvc.perform(get("/api/v1/orders/{id}", response.orderId())
                .accept(MediaType.APPLICATION_JSON))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.orderId").value(response.orderId().toString()))
            .andExpect(jsonPath("$.status").value("PENDING"));
    }

    @Test
    void createOrder_invalidBody_returns422WithViolations() throws Exception {
        var invalidRequest = new CreateOrderRequest(null, List.of());

        mockMvc.perform(post("/api/v1/orders")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(invalidRequest)))
            .andExpect(status().isUnprocessableEntity())
            .andExpect(jsonPath("$.violations").isArray());
    }
}
```

### Spring Data JPA Repository Tests

```java
@DataJpaTest
@Testcontainers
class OrderRepositoryIT {

    @Container
    static PostgreSQLContainer<?> db = new PostgreSQLContainer<>("postgres:16-alpine");

    @DynamicPropertySource
    static void properties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", db::getJdbcUrl);
        registry.add("spring.datasource.username", db::getUsername);
        registry.add("spring.datasource.password", db::getPassword);
    }

    @Autowired
    private OrderRepository orderRepository;

    @Test
    void findByIdWithLineItems_orderWithItems_fetchesEagerly() {
        // given: pre-seeded test data via @Sql or test fixture
        UUID orderId = UUID.fromString("...");

        // when
        Optional<Order> result = orderRepository.findByIdWithLineItems(orderId);

        // then
        assertThat(result).isPresent();
        assertThat(result.get().getLineItems()).hasSize(2);
    }
}
```

### Test Data — Factory Pattern

```java
// Use static factory methods or builder pattern — never raw `new` in test body
public final class OrderTestFactory {

    private OrderTestFactory() {}

    public static Order pendingOrder() {
        return Order.builder()
            .id(UUID.randomUUID())
            .customerId(UUID.randomUUID())
            .status(OrderStatus.PENDING)
            .createdAt(Instant.now())
            .lineItems(List.of(lineItem()))
            .build();
    }

    public static CreateOrderRequest validCreateRequest() {
        return new CreateOrderRequest(UUID.randomUUID(), List.of(lineItemRequest()));
    }
}
```

---

## Angular / TypeScript Test Standards

### Component Tests

```typescript
describe('CustomerListComponent', () => {
  let component: CustomerListComponent;
  let fixture: ComponentFixture<CustomerListComponent>;
  let customerServiceSpy: jasmine.SpyObj<CustomerService>;

  beforeEach(async () => {
    customerServiceSpy = jasmine.createSpyObj('CustomerService', ['getAll']);
    customerServiceSpy.getAll.and.returnValue(of([{ id: '1', name: 'Alice' }]));

    await TestBed.configureTestingModule({
      imports: [CustomerListComponent],
      providers: [{ provide: CustomerService, useValue: customerServiceSpy }],
    }).compileComponents();

    fixture = TestBed.createComponent(CustomerListComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should display customers returned by service', () => {
    const items = fixture.nativeElement.querySelectorAll('.customer-list__item');
    expect(items.length).toBe(1);
    expect(items[0].textContent).toContain('Alice');
  });

  it('should call getAll on init', () => {
    expect(customerServiceSpy.getAll).toHaveBeenCalledTimes(1);
  });
});
```

### Service Tests

```typescript
describe('CustomerService', () => {
  let service: CustomerService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [CustomerService],
    });
    service = TestBed.inject(CustomerService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  afterEach(() => httpMock.verify());

  it('getAll should return typed customer array', () => {
    const mockCustomers: Customer[] = [{ id: '1', name: 'Alice', active: true }];

    service.getAll().subscribe(customers => {
      expect(customers).toEqual(mockCustomers);
    });

    const req = httpMock.expectOne('/api/v1/customers');
    expect(req.request.method).toBe('GET');
    req.flush(mockCustomers);
  });
});
```

---

## Coverage Requirements

- **Business logic classes:** 80% line coverage, 70% branch coverage minimum
- **Controllers:** All endpoints tested for happy path + at least one error case
- **Repositories:** All custom queries tested
- **Every `if` condition:** Must have at least one true-branch and one false-branch test
- **Every `catch` block:** Must have a test that triggers the exception
- **Every `Optional.empty()` path:** Must have a corresponding test

---

## What Copilot Must NOT Generate in Tests

| Anti-Pattern | Why |
|-------------|-----|
| `Thread.sleep(1000)` | Use `Awaitility` — sleep is flaky |
| Empty `@Test` methods | A test with no assertions is misleading |
| `assertTrue(true)` or `assertNotNull(result)` only | Must assert meaningful business outcomes |
| Mocking the class under test | Defeats the purpose of the test |
| `@SpringBootTest` for a pure unit test | Use slices or pure Mockito — full context is slow |
| Test methods sharing mutable state | Tests must be independent and order-agnostic |
| Raw `new` in test body for domain objects | Use factory methods or builders |
| Testing private methods directly | Test behaviour through the public API |
| Ignoring or disabling tests without explanation | `@Disabled` requires a comment with a tracking issue |
