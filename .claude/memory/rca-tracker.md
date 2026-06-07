# RCA Tracker

> Tracks open and resolved incidents. Full post-mortems live in `knowledge/incident-repository/`.

---

## Open Incidents

| ID | Summary | Severity | Opened | Status | Owner |
|----|---------|----------|--------|--------|-------|
| <!-- INC-NNN --> | <!-- summary --> | <!-- P1-P4 --> | | RCA Pending | |

---

## Incidents In Progress (RCA Open)

| ID | Summary | Severity | RCA Due | Blocking |
|----|---------|----------|---------|---------|
| <!-- INC-NNN --> | | | | |

---

## Resolved Incidents

| ID | Summary | Severity | Resolved | RCA Link | Corrective Actions |
|----|---------|----------|---------|----------|-------------------|
| [INC-001](../../knowledge/incident-repository/INC-001-outbox-publisher-backlog.md) | Outbox publisher backlog — 2h delay | P2 | 2025-01-22 | ✅ | 3 of 5 done |

---

## RCA Process

```
Incident Declared
    ↓
Mitigation Applied (≤ 15 min for P1)
    ↓
Timeline Reconstructed
    ↓
5-Whys Root Cause Analysis
    ↓
Corrective Actions Defined (with owners + due dates)
    ↓
Post-Mortem Published (knowledge/incident-repository/)
    ↓
Corrective Actions Tracked Here
    ↓
Close when all corrective actions done
```

## Commands

- `/rca "symptoms"` — Open RCA workflow
- `/incident "severity: P1, service: name, symptom: description"` — Declare incident
- `/capture-incident "summary"` — Capture post-mortem into knowledge repository
