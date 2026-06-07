# Incident Repository

Post-mortem learnings from production incidents across EEIK-managed projects.

**Purpose:** Every incident should make the next one less likely. Learnings here inform runbooks, monitoring, and architecture decisions.

## How to Contribute

After a P1/P2 incident is resolved, run:

```
/capture-incident "brief symptom description"
```

The RCA agent will guide the post-mortem.

## Index

| ID | Summary | Severity | RCA Complete | Corrective Actions Done |
|----|---------|----------|-------------|------------------------|
| [INC-001](INC-001-outbox-publisher-backlog.md) | Outbox event backlog caused 2h message delay | P2 | ✅ | 🔄 In Progress |

## Severity Definitions

| Level | Impact | Response SLA |
|-------|--------|-------------|
| P1 | Complete service outage or data loss | 15 min |
| P2 | Degraded service or significant delay | 30 min |
| P3 | Minor degradation, no data impact | 2 hours |
| P4 | Cosmetic or low-impact | Next sprint |
