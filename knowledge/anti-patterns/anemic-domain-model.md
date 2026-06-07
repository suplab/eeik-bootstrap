# Anti-Pattern: Anemic Domain Model

**Severity:** HIGH  
**Domain:** Domain-Driven Design  
**Commonly Found In:** Spring Boot services where JPA entities double as DTOs

---

## What It Looks Like

```java
// ❌ ANEMIC — entity has no behaviour, only getters/setters
@Entity
public class Order {
    private UUID id;
    private OrderStatus status;
    private BigDecimal total;

    public UUID getId() { return id; }
    public void setId(UUID id) { this.id = id; }
    public OrderStatus getStatus() { return status; }
    public void setStatus(OrderStatus status) { this.status = status; }
    public BigDecimal getTotal() { return total; }
    public void setTotal(BigDecimal total) { this.total = total; }
}

// ❌ Business logic leaks into the service layer
@Service
public class OrderService {
    public void cancelOrder(UUID orderId) {
        Order order = orderRepository.findById(orderId).orElseThrow();
        if (order.getStatus() == OrderStatus.SHIPPED) {
            throw new IllegalStateException("Cannot cancel shipped order");
        }
        order.setStatus(OrderStatus.CANCELLED);  // ← domain rule in service
    }
}
```

## Why It Fails

- Business rules scattered across service classes — no single source of truth
- Entity is just a data bag — OOP advantage lost
- Rules are duplicated when multiple services manipulate the same aggregate
- Impossible to enforce invariants — any code can put the entity into an invalid state
- Unit tests become complex integration tests (require Spring context)

## Correct Alternative

```java
// ✅ RICH DOMAIN MODEL — behaviour lives with the data
@Entity
public class Order {
    private UUID id;
    private OrderStatus status;
    private BigDecimal total;

    // Domain behaviour on the aggregate
    public void cancel() {
        if (this.status == OrderStatus.SHIPPED) {
            throw new DomainException("Cannot cancel an order that has already shipped");
        }
        this.status = OrderStatus.CANCELLED;
    }

    public boolean isCancellable() {
        return this.status == OrderStatus.PENDING || this.status == OrderStatus.CONFIRMED;
    }
    // No public setters — state changes only through domain methods
}

// ✅ Service delegates to the aggregate
@Service
public class OrderService {
    public void cancelOrder(UUID orderId) {
        Order order = orderRepository.findById(orderId).orElseThrow();
        order.cancel();   // ← domain rule in the aggregate
        orderRepository.save(order);
    }
}
```

## Detection

- Every entity field has a public setter → likely anemic
- Service classes are 500+ lines with `if` chains → domain logic leak
- Same business rule appears in multiple services → leaked invariant
