---
applyTo: "**/src/main/java/**/*.java"
---

## Context

This instruction file applies to all Spring Boot Java source files. The project uses Spring Boot 3.x as its baseline, which requires Java 17+ and the Jakarta EE namespace (`jakarta.*`). New microservices target Java 21 and should leverage virtual threads and modern language features where appropriate. This context layer does not apply to legacy Spring MVC modules — see `java-legacy.instructions.md` for those.

---

## Coding Standards

- **Java version baseline:** Java 17 minimum; prefer Java 21 features where the runtime supports them
- **Namespace:** Always `jakarta.*` — never `javax.*` in Spring Boot 3.x code
- **Records over POJOs:** Use Java records for immutable DTOs and value objects; reserve classes for mutable entities
- **Text blocks:** Use `"""..."""` for multi-line SQL, JSON, or HTML strings
- **Pattern matching:** Use `instanceof` pattern matching (`if (obj instanceof MyType t)`) — no explicit cast
- **Sealed types:** Use sealed interfaces/classes to model closed domain hierarchies
- **Switch expressions:** Use `switch` expressions with arrow syntax over `switch` statements
- **Constructor injection only:** All Spring-managed beans use constructor injection; mark injected fields `final`
- **No field `@Autowired`:** Never inject via `@Autowired` on a field — it hides dependencies and breaks testability
- **`@ConfigurationProperties`:** All externalized config must use a typed `@ConfigurationProperties` class — no `@Value` on individual fields for grouped config
- **`application.yml`:** Always YAML, never `.properties` format — structure config hierarchically

---

## Preferred Patterns

### REST Controller

```java
@RestController
@RequestMapping("/api/v1/orders")
@Tag(name = "Orders", description = "Order management endpoints")
@RequiredArgsConstructor  // if Lombok is already on classpath; else write constructor manually
public class OrderController {

    private final OrderService orderService;

    @GetMapping("/{id}")
    @Operation(summary = "Retrieve an order by ID")
    public ResponseEntity<OrderResponse> getOrder(@PathVariable UUID id) {
        return ResponseEntity.ok(orderService.findById(id));
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public OrderResponse createOrder(@Valid @RequestBody CreateOrderRequest request) {
        return orderService.create(request);
    }
}
```

### Exception Handling (RFC 7807 ProblemDetail)

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(OrderNotFoundException.class)
    public ProblemDetail handleOrderNotFound(OrderNotFoundException ex) {
        ProblemDetail problem = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        problem.setTitle("Order Not Found");
        return problem;
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ProblemDetail handleValidation(MethodArgumentNotValidException ex) {
        ProblemDetail problem = ProblemDetail.forStatus(HttpStatus.UNPROCESSABLE_ENTITY);
        problem.setTitle("Validation Failed");
        problem.setProperty("violations", ex.getBindingResult().getFieldErrors()
            .stream().map(e -> e.getField() + ": " + e.getDefaultMessage()).toList());
        return problem;
    }
}
```

### Spring Data JPA Repository

```java
public interface OrderRepository extends JpaRepository<Order, UUID> {

    List<Order> findByCustomerIdAndStatus(UUID customerId, OrderStatus status);

    @Query("SELECT o FROM Order o JOIN FETCH o.lineItems WHERE o.id = :id")
    Optional<Order> findByIdWithLineItems(@Param("id") UUID id);

    Page<Order> findByCreatedAtAfter(Instant cutoff, Pageable pageable);
}
```

### Spring Security 6.x Configuration

```java
@Configuration
@EnableMethodSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http
            .csrf(AbstractHttpConfigurer::disable)
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/actuator/health", "/actuator/info").permitAll()
                .requestMatchers("/api/**").authenticated()
                .anyRequest().denyAll()
            )
            .oauth2ResourceServer(oauth2 -> oauth2.jwt(Customizer.withDefaults()))
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .build();
    }
}
```

### Typed Configuration Properties

```java
@ConfigurationProperties(prefix = "app.payment")
public record PaymentProperties(
    String gatewayUrl,
    Duration timeout,
    int maxRetries
) {}
```

```yaml
# application.yml
app:
  payment:
    gateway-url: https://payment.example.com
    timeout: 30s
    max-retries: 3
```

### Java 21 Virtual Threads

```java
@Bean
public TomcatProtocolHandlerCustomizer<?> virtualThreadsCustomizer() {
    return handler -> handler.setExecutor(Executors.newVirtualThreadPerTaskExecutor());
}
```

---

## Anti-Patterns — Do NOT Generate

```java
// WRONG: field injection
@Autowired
private OrderService orderService;

// WRONG: javax namespace in Spring Boot 3
import javax.persistence.Entity;

// WRONG: WebSecurityConfigurerAdapter (removed in Spring Security 6)
public class SecurityConfig extends WebSecurityConfigurerAdapter { ... }

// WRONG: @Value on individual fields for grouped config
@Value("${app.payment.gateway-url}")
private String gatewayUrl;

// WRONG: returning null from service methods — use Optional or throw
public Order findOrder(UUID id) {
    return repository.findById(id).orElse(null);
}

// WRONG: catching and swallowing exceptions
try {
    processPayment(order);
} catch (Exception e) {
    // silent swallow
}

// WRONG: application.properties file
# app.payment.gateway-url=https://...
```

---

## Dependencies & Versions

| Library | Version | Import Style |
|---------|---------|-------------|
| Spring Boot | 3.2.x+ | `org.springframework.boot` |
| Spring Security | 6.x | `org.springframework.security` |
| Spring Data JPA | 3.x | `org.springframework.data.jpa` |
| springdoc-openapi | 2.x | `org.springdoc` |
| MapStruct | 1.5.x | `org.mapstruct` |
| Jakarta EE | 10 | `jakarta.*` |
| Java | 17 / 21 | — |

---

## Test Conventions

- Use `@WebMvcTest(MyController.class)` for controller layer tests — not full `@SpringBootTest`
- Use `@DataJpaTest` for repository tests — auto-configures H2 or Testcontainers
- Use `@MockBean` to inject mocked dependencies in slice tests
- Use `MockMvc` with `ObjectMapper` for controller request/response verification
- Name integration test classes with suffix `IT` — they run in a separate Maven phase
- Use `@Testcontainers` + `@Container` for tests requiring a real database or message broker
