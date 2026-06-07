# Anti-Pattern: Shared Database

**Severity:** CRITICAL  
**Domain:** Microservices / DDD  
**Commonly Found In:** Microservice architectures migrated from monoliths

---

## What It Looks Like

```
Service A ──┐
             ├──▶  single_database (shared schema)
Service B ──┘
```

Both services read and write the same tables directly. Service A queries Service B's tables without going through an API.

## Why It Fails

- **Schema coupling:** Changing a column in Service B's table breaks Service A
- **Deployment coupling:** Services cannot be deployed independently
- **Ownership ambiguity:** No clear owner for data integrity
- **Scaling conflict:** Services compete for database connections
- **Testing impossibility:** Cannot test services in isolation

## Correct Alternative

Each service owns its schema exclusively. Cross-service data access goes through APIs or async events.

```
Service A ──▶ database_a (owns its own schema)
                   │
                   │ (async event when data changes)
                   ▼
             Event Bus (SQS / Kafka / EventBridge)
                   │
                   ▼
Service B ──▶ database_b (maintains its own read model)
```

- **Never** use database joins across service boundaries
- **Never** have two services write to the same table
- Cross-context queries: publish events, maintain local read models (CQRS)
- For synchronous needs: REST or gRPC APIs with clear ownership

## Detection

- `JOIN` between tables from different bounded contexts
- Foreign keys from one service's tables to another's
- Multiple services in the same database schema/user
