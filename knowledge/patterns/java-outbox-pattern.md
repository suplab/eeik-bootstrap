# Pattern: Outbox Pattern (Transactional Event Publishing)

**Status:** Approved  
**Domain:** Distributed Systems  
**Technology:** Java 21 / Spring Boot 3.x / PostgreSQL / SQS or Kafka  
**Validated:** Multiple production projects

---

## Context

When a service needs to publish a domain event AND persist a database change in the same logical operation, naive approaches fail:

- **Problem A:** Publish event THEN save to DB — event published but DB write fails → event without data
- **Problem B:** Save to DB THEN publish event — DB saved but publish fails → data without event
- **Root cause:** Two-phase commit across DB and message broker is complex and fragile

## Solution

Write the event to an `outbox_events` table **in the same DB transaction** as the business data. A separate scheduled process reads pending outbox events and publishes them to the broker, then marks them as processed.

```
Business transaction:
  ┌──────────────────────────────────────────┐
  │  INSERT INTO orders (...)              DB │
  │  INSERT INTO outbox_events (payload)   DB │
  └──────────────────────────────────────────┘
  (atomic — either both succeed or both fail)

Outbox publisher (separate thread/scheduler):
  SELECT * FROM outbox_events WHERE status = 'PENDING'
       ↓
  publish to SQS/Kafka
       ↓
  UPDATE outbox_events SET status = 'PROCESSED'
```

## Example Implementation

### Entity

```java
@Entity
@Table(name = "outbox_events")
public class OutboxEvent {

    @Id
    @GeneratedValue
    private UUID id;

    @Column(nullable = false)
    private String aggregateType;      // e.g. "Order"

    @Column(nullable = false)
    private String aggregateId;        // e.g. order UUID

    @Column(nullable = false)
    private String eventType;          // e.g. "OrderPlaced"

    @Column(nullable = false, columnDefinition = "jsonb")
    private String payload;

    @Enumerated(EnumType.STRING)
    private OutboxStatus status = OutboxStatus.PENDING;

    private Instant occurredAt = Instant.now();
    private Instant processedAt;

    public void markProcessed() {
        this.status = OutboxStatus.PROCESSED;
        this.processedAt = Instant.now();
    }
}
```

### Repository

```java
public interface OutboxEventRepository extends JpaRepository<OutboxEvent, UUID> {

    @Lock(LockModeType.PESSIMISTIC_WRITE)
    List<OutboxEvent> findTop100ByStatusOrderByOccurredAtAsc(OutboxStatus status);
}
```

### Publisher (Scheduled)

```java
@Component
@RequiredArgsConstructor
@Slf4j
public class OutboxEventPublisher {

    private final OutboxEventRepository outboxRepository;
    private final SqsTemplate sqsTemplate;
    private final String queueUrl;

    @Scheduled(fixedDelay = 1000)
    @Transactional
    public void publishPendingEvents() {
        List<OutboxEvent> pending = outboxRepository
            .findTop100ByStatusOrderByOccurredAtAsc(OutboxStatus.PENDING);

        for (OutboxEvent event : pending) {
            try {
                sqsTemplate.send(queueUrl, event.getPayload());
                event.markProcessed();
                log.info("Published outbox event: type={}, id={}", event.getEventType(), event.getId());
            } catch (Exception e) {
                log.error("Failed to publish outbox event id={}: {}", event.getId(), e.getMessage());
                // Leave as PENDING — will retry on next schedule tick
            }
        }
    }
}
```

### Migration

```sql
-- V{n}__create_outbox_events.sql
CREATE TABLE outbox_events (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    aggregate_type VARCHAR(100) NOT NULL,
    aggregate_id   VARCHAR(255) NOT NULL,
    event_type     VARCHAR(200) NOT NULL,
    payload        JSONB        NOT NULL,
    status         VARCHAR(20)  NOT NULL DEFAULT 'PENDING',
    occurred_at    TIMESTAMP    NOT NULL DEFAULT NOW(),
    processed_at   TIMESTAMP
);

CREATE INDEX idx_outbox_status ON outbox_events(status, occurred_at)
    WHERE status = 'PENDING';
```

## Trade-offs

| Pro | Con |
|-----|-----|
| Guarantees at-least-once delivery | Consumer must be idempotent |
| No distributed transaction needed | Adds latency (up to 1s by default) |
| Survives publisher crashes | Requires cleanup job for old events |
| Simple to implement and debug | Dead-letter handling needs design |

## When NOT to Use

- When events don't need guaranteed delivery (fire-and-forget metrics)
- When the broker supports XA transactions natively and latency budget allows
- When using AWS EventBridge Pipes with direct RDS source (managed alternative)

## Related Patterns

- Idempotent Consumer (pair with this pattern on the subscriber side)
- Dead Letter Queue (for failed publish retry exhaustion)
- Event Sourcing (stronger variant — event IS the state)
