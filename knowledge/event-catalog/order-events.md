# Order Domain Events

Owner: order-service  
Broker: SQS / EventBridge  
Encoding: JSON

---

## order.placed

Published when a new order is successfully created and persisted.

**Schema v1.0**

```json
{
  "eventId":       "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "eventType":     "order.placed",
  "aggregateType": "Order",
  "aggregateId":   "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "occurredAt":    "2025-01-15T14:30:00.000Z",
  "version":       "1.0",
  "source":        "order-service",
  "correlationId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "payload": {
    "customerId":  "uuid",
    "items": [
      { "productId": "uuid", "quantity": 2, "unitPrice": 29.99, "currency": "GBP" }
    ],
    "totalAmount":  59.98,
    "currency":     "GBP",
    "deliveryAddress": {
      "line1": "string",
      "city":  "string",
      "postcode": "string",
      "country": "GB"
    }
  }
}
```

**Consumers:**
- `payment-service` → initiates payment authorisation
- `inventory-service` → reserves stock
- `notification-service` → sends order confirmation email

**SLO:** Published within 2 seconds of order persistence.

---

## order.cancelled

Published when an order is cancelled by the customer or system.

**Schema v1.0**

```json
{
  "eventType": "order.cancelled",
  "payload": {
    "orderId":       "uuid",
    "customerId":    "uuid",
    "cancellationReason": "CUSTOMER_REQUEST | PAYMENT_FAILED | STOCK_UNAVAILABLE",
    "cancelledAt":   "ISO-8601"
  }
}
```

**Consumers:**
- `payment-service` → initiates refund if payment was taken
- `inventory-service` → releases reserved stock
- `notification-service` → sends cancellation confirmation
