# INC-001: Outbox Event Publisher Backlog — 2h Message Delay

**Severity:** P2  
**Duration:** 2 hours 14 minutes  
**Service:** order-service  
**Date:** 2025-01-22  
**RCA Status:** Complete  

---

## Summary

The Outbox event publisher stopped processing events for 2+ hours due to a database connection pool exhaustion. Orders were being placed successfully (writes succeeded) but downstream services (payment-service, inventory-service) received events 2+ hours late.

## Impact

- ~1,400 orders affected (placed but payment not initiated for 2h)
- No data loss — all events were safely stored in outbox_events table
- Customer-visible: order confirmation emails delayed by 2h
- Payment processing delayed for 1,400 orders

## Timeline

```
14:05 — Spike in order volume (Black Friday campaign launch)
14:12 — DB connection pool hits maximum (HikariCP pool_size=10, all connections active)
14:13 — Outbox publisher @Scheduled method fails to acquire connection, throws silently
14:13 — No alert fires (outbox lag not monitored)
14:15 — payment-service team notices no new payment events
14:30 — order-service team paged
14:45 — Root cause identified (pool exhaustion visible in HikariCP metrics)
15:00 — Connection pool size increased to 30; outbox publisher resumes
16:19 — Backlog fully drained
```

## Root Cause (5-Whys)

1. **Why** were events delayed? Outbox publisher failed to acquire DB connection.
2. **Why** did it fail to acquire a connection? Connection pool was exhausted.
3. **Why** was the pool exhausted? Campaign launch tripled order volume; each order used a connection.
4. **Why** wasn't the pool sized for peak? No load testing was done at campaign volume.
5. **Why** did no alert fire? Outbox queue depth was not monitored.

**Root Cause:** Outbox publisher had no visibility, no alerting, and no backpressure — it silently failed under load.

## Corrective Actions

| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| Add CloudWatch alarm: outbox_events pending > 100 for 5 min | Platform team | 2025-01-29 | ✅ Done |
| Load test outbox publisher at 3× peak order volume | order-service team | 2025-02-05 | 🔄 Scheduled |
| Add HikariCP connection wait timeout metric alert | Platform team | 2025-01-29 | ✅ Done |
| Document DB pool sizing formula in aws-standard.md | Architecture | 2025-02-01 | 🔄 In Progress |
| Add outbox lag to the Service Level Indicator set | SRE team | 2025-02-05 | ⬜ Planned |

## Prevention

Added to `knowledge/patterns/java-outbox-pattern.md`:
- Monitor `outbox_events WHERE status = 'PENDING'` count as a key SLI
- Alert when queue depth exceeds 100 events (5-minute window)
- Size connection pool for peak + 50% headroom, not average load
- Load test outbox publisher before every major campaign

## Runbook Created

`docs/runbooks/outbox-publisher-backlog.md` — steps to diagnose and resolve outbox backlog.
