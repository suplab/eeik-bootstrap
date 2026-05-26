---
applyTo: "src/main/java/**/*.java"
---

## Context

This instruction file applies to legacy Java modules built on Spring 4.x or 5.x using the traditional XML + annotation configuration model. These modules are servlet-based, use `JdbcTemplate` for data access (not Spring Data JPA), and are packaged as WAR files deployed to an application server (e.g., IBM WebSphere, JBoss EAP, Apache Tomcat). The Java baseline is Java 8 or Java 11. Do not apply Spring Boot auto-configuration assumptions to this code.

---

## Coding Standards

- **Java version:** Java 8 or 11 — do not use records, sealed classes, text blocks, `var` (Java 11 only with care), or any feature requiring Java 14+
- **Spring version:** Spring 4.x or 5.x — use `org.springframework` imports, not `org.springframework.boot`
- **No Spring Boot:** Do not generate `@SpringBootApplication`, `@EnableAutoConfiguration`, or any `spring-boot` dependency references
- **No Spring Data JPA:** Use `JdbcTemplate` or `NamedParameterJdbcTemplate` for all data access
- **XML config awareness:** Be aware that `applicationContext.xml` and `dispatcher-servlet.xml` may define beans; do not duplicate them in Java config without understanding the existing XML
- **Transaction management:** Use `@Transactional` with explicit `propagation` and `rollbackFor` — never rely on defaults silently
- **Constructor or setter injection:** Field injection is legacy-acceptable but constructor injection is preferred for new code
- **Logging:** SLF4J + Logback (or Log4j2) — never `System.out.println`

---

## Preferred Patterns

### Spring MVC Controller (Legacy)

```java
@Controller
@RequestMapping("/customers")
public class CustomerController {

    private static final Logger log = LoggerFactory.getLogger(CustomerController.class);

    private final CustomerService customerService;

    public CustomerController(CustomerService customerService) {
        this.customerService = customerService;
    }

    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public String getCustomer(@PathVariable Long id, Model model) {
        log.debug("Fetching customer with id {}", id);
        model.addAttribute("customer", customerService.findById(id));
        return "customer/detail";
    }

    @RequestMapping(value = "/", method = RequestMethod.POST)
    public String createCustomer(@Valid @ModelAttribute CustomerForm form,
                                 BindingResult result, RedirectAttributes redirect) {
        if (result.hasErrors()) {
            return "customer/form";
        }
        customerService.create(form);
        redirect.addFlashAttribute("message", "Customer created successfully");
        return "redirect:/customers/";
    }
}
```

### REST Endpoint (Legacy Spring MVC)

```java
@Controller
@RequestMapping("/api/orders")
public class OrderRestController {

    private final OrderService orderService;

    public OrderRestController(OrderService orderService) {
        this.orderService = orderService;
    }

    @RequestMapping(value = "/{id}", method = RequestMethod.GET,
                    produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public OrderDto getOrder(@PathVariable Long id) {
        return orderService.findById(id);
    }
}
```

### JdbcTemplate Data Access

```java
@Repository
public class OrderJdbcRepository {

    private static final Logger log = LoggerFactory.getLogger(OrderJdbcRepository.class);

    private final NamedParameterJdbcTemplate jdbc;

    public OrderJdbcRepository(NamedParameterJdbcTemplate jdbc) {
        this.jdbc = jdbc;
    }

    public Optional<Order> findById(Long id) {
        String sql = "SELECT o.id, o.customer_id, o.status, o.created_at " +
                     "FROM SCHEMA.ORDERS o WHERE o.id = :id";
        MapSqlParameterSource params = new MapSqlParameterSource("id", id);
        try {
            Order order = jdbc.queryForObject(sql, params, new OrderRowMapper());
            return Optional.ofNullable(order);
        } catch (EmptyResultDataAccessException e) {
            log.debug("Order {} not found", id);
            return Optional.empty();
        }
    }
}
```

### Transaction Management

```java
@Service
@Transactional(readOnly = true)
public class OrderService {

    @Transactional(propagation = Propagation.REQUIRED, rollbackFor = Exception.class)
    public Order createOrder(CreateOrderRequest request) {
        // transactional write operation
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW, rollbackFor = Exception.class)
    public void processPayment(Long orderId) {
        // runs in its own transaction — outer transaction is suspended
    }
}
```

### Exception Handling (Legacy HandlerExceptionResolver)

```java
@Component
public class GlobalExceptionHandler implements HandlerExceptionResolver {

    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @Override
    public ModelAndView resolveException(HttpServletRequest request,
                                         HttpServletResponse response,
                                         Object handler, Exception ex) {
        log.error("Unhandled exception on {} {}", request.getMethod(), request.getRequestURI(), ex);
        response.setStatus(HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
        return new ModelAndView("error/500");
    }
}
```

### Maven Multi-Module Structure

```xml
<!-- Parent pom.xml -->
<project>
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>my-app-core</module>
        <module>my-app-web</module>
        <module>my-app-service</module>
    </modules>

    <dependencyManagement>
        <dependencies>
            <!-- Centralize versions here -->
        </dependencies>
    </dependencyManagement>
</project>
```

### MapStruct Mapper (Legacy)

```java
@Mapper(componentModel = "spring")
public interface CustomerMapper {

    CustomerDto toDto(Customer customer);

    Customer toEntity(CustomerDto dto);

    List<CustomerDto> toDtoList(List<Customer> customers);
}
```

---

## Anti-Patterns — Do NOT Generate

```java
// WRONG: Spring Boot annotations in a legacy module
@SpringBootApplication
public class MyApp { ... }

// WRONG: Spring Data JPA in a JdbcTemplate project
public interface OrderRepository extends JpaRepository<Order, Long> { ... }

// WRONG: javax.persistence imports where JdbcTemplate is the pattern
import javax.persistence.Entity;

// WRONG: @Autowired field injection (prefer constructor injection)
@Autowired
private CustomerService customerService;

// WRONG: raw JDBC Statement with string concatenation (SQL injection risk)
Statement stmt = conn.createStatement();
stmt.execute("SELECT * FROM ORDERS WHERE ID = " + id);

// WRONG: new Java features incompatible with Java 8 target
record Customer(Long id, String name) {}  // Java 16+ — not available

// WRONG: silently swallowing exceptions
try {
    service.process(request);
} catch (Exception e) {
    // do nothing
}
```

---

## Dependencies & Versions

| Library | Version | Notes |
|---------|---------|-------|
| Spring Framework | 4.3.x or 5.3.x | `org.springframework` |
| Spring Security | 4.x or 5.x | `WebSecurityConfigurerAdapter` still valid |
| JdbcTemplate | (with Spring) | `org.springframework.jdbc` |
| MapStruct | 1.5.x | Annotation processor — declare in `<build><plugins>` |
| Jackson | 2.x | `com.fasterxml.jackson` |
| SLF4J + Logback | 1.7.x / 1.2.x | `org.slf4j`, `ch.qos.logback` |
| Java | 8 or 11 | No records, no sealed classes |

---

## Test Conventions

- Use `@RunWith(SpringJUnit4ClassRunner.class)` or `@ExtendWith(SpringExtension.class)` (JUnit 5)
- Spring MVC tests: `MockMvc` with `standaloneSetup` or `@WebMvcTest`
- JdbcTemplate tests: Use an embedded H2 database with a test schema script
- Test configuration: Use `@ContextConfiguration` pointing to test-specific XML or `@TestConfiguration` classes
- Name unit tests `*Test.java`, integration tests `*IT.java`
- Avoid starting the full application context in unit tests — use `Mockito` mocks
