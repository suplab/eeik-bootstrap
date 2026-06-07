# Event Catalog

Canonical definitions for domain events used across EEIK-managed services.

## Naming Convention

```
{aggregate}.{past-tense-verb}

Examples:
  order.placed
  payment.authorised
  claim.submitted
  policy.renewed
  user.registered
```

## Event Envelope Standard

All domain events share this envelope:

```json
{
  "eventId":       "uuid-v4",
  "eventType":     "order.placed",
  "aggregateType": "Order",
  "aggregateId":   "uuid-v4",
  "occurredAt":    "2025-01-01T10:00:00.000Z",
  "version":       "1.0",
  "source":        "order-service",
  "correlationId": "uuid-v4",
  "payload":       { ... }
}
```

## Versioning

- Events are immutable once published
- Breaking changes require a new version: `order.placed.v2`
- Old and new versions coexist during transition
- Consumers must tolerate unknown fields (be liberal in what you accept)

## Index

| Event | Owner Service | Description | Schema |
|-------|---------------|-------------|--------|
| [order.placed](order-events.md#order-placed) | order-service | A new order was successfully created | v1.0 |
| [payment.authorised](payment-events.md#payment-authorised) | payment-service | A payment was authorised by the issuer | v1.0 |
| [claim.submitted](claim-events.md#claim-submitted) | claims-service | A new insurance claim was submitted | v1.0 |

## Adding New Events

1. Define the event schema in this catalog first
2. Get review from the owning team's architect
3. Add to the async integration contract tests (Pact)
4. Publish the event from the producing service
5. Update all consumers to handle the new event type
