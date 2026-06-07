# Tech Debt Register

> Tracks known technical debt with priority and target resolution.
> 
> **Format:** Use `/memory-update` to log new debt items. Review and prioritise in sprint planning.

---

## Priority Levels

| Priority | Definition | Expected Resolution |
|----------|-----------|-------------------|
| P0 | Blocking further development or causing production risk | Current sprint |
| P1 | High impact — slowing delivery significantly | Within 2 sprints |
| P2 | Medium impact — manageable but growing | Within quarter |
| P3 | Low impact — good to fix when passing | Backlog |

---

## Active Debt

| ID | Description | Priority | Added | Target Sprint | Owner |
|----|------------|----------|-------|---------------|-------|
| TD-001 | `optional-get-without-check` — several places use `Optional.get()` without guard | P2 | <!-- date --> | <!-- sprint --> | <!-- team --> |
| TD-002 | Missing Testcontainers integration tests for messaging layer | P1 | <!-- date --> | <!-- sprint --> | <!-- team --> |
| TD-003 | CDK stacks not parameterised for multi-region — hardcoded `eu-west-1` in 3 places | P2 | <!-- date --> | <!-- sprint --> | <!-- team --> |
| <!-- TD-NNN --> | <!-- description --> | <!-- P0-P3 --> | | | |

---

## Resolved Debt

| ID | Description | Resolved | PR/Commit |
|----|------------|---------|-----------|
| <!-- TD-NNN --> | <!-- description --> | <!-- date --> | <!-- link --> |

---

## Debt Rules

1. Every debt item must have an owner — no orphaned debt
2. P0 debt is never deferred — fix in current sprint or pause new feature work
3. Debt register reviewed in every sprint planning
4. When fixing debt, add a test to prevent regression
