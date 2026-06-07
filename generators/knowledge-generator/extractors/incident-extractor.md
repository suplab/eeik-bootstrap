# Incident Extractor

## Purpose

Convert an incident post-mortem into a structured RCA entry.

Activated by `/capture-incident "summary"`.

Works alongside the `/rca "symptoms"` command — `/rca` drives the live investigation; `/capture-incident` captures the learnings afterward.

---

## Input

The user provides:

- A description of the incident symptoms
- The timeline (if available)
- The root cause (if already identified)
- Corrective actions (if already defined)

---

## Extraction Process

### Step 1 — Classify the Incident

Severity: P1 | P2 | P3 | P4

Determine the next INC-NNN number from `knowledge/incident-repository/README.md`.

### Step 2 — Build the Timeline

Ask the user to walk through the timeline chronologically:
- When was the first indication of the problem?
- When was it escalated?
- When was the root cause identified?
- When was the mitigation applied?
- When was full recovery confirmed?

### Step 3 — Apply 5-Whys

Facilitate 5-Whys analysis:

```
Why did X happen?
  → Because Y
Why did Y happen?
  → Because Z
Why did Z happen?
  → Because ...
(Continue until systemic root cause identified)
```

### Step 4 — Define Corrective Actions

For each corrective action, capture:
- Description
- Owner
- Due date

### Step 5 — Identify Prevention Opportunities

Ask: "What monitoring, alerting, or architectural change would have prevented this or reduced MTTR?"

### Step 6 — Write the File

Write to `knowledge/incident-repository/INC-{NNN}-{kebab-case-summary}.md`.

### Step 7 — Update Trackers

- Add to `knowledge/incident-repository/README.md` index
- Update `.claude/memory/rca-tracker.md` resolved section

---

## Output Format

```markdown
# INC-NNN: {Summary}

**Severity:** P1 | P2 | P3 | P4
**Duration:** {total minutes/hours}
**Service:** {service name}
**Date:** YYYY-MM-DD
**RCA Status:** Complete

---

## Summary
## Impact
## Timeline
## Root Cause (5-Whys)
## Corrective Actions
| Action | Owner | Due Date | Status |
## Prevention
## Runbook Created
```
