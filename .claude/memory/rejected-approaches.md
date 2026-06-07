# Rejected Approaches

> Things that were considered and explicitly rejected. Prevents teams from re-litigating the same decisions or re-trying approaches that failed.

---

## Format

```
### RA-NNN: {Approach Name}
**Date:** YYYY-MM-DD
**Proposed By:** team/person
**Rejected By:** architect/team
**Reason:** Why it was rejected
**Alternative Chosen:** What was done instead
**Retry Condition:** Under what circumstances this could be reconsidered (if any)
```

---

## Rejected Approaches

### RA-001: Field Injection (`@Autowired` on fields)
**Date:** Platform baseline  
**Proposed By:** Legacy Spring patterns  
**Rejected By:** EEIK Architecture Guild  
**Reason:** Fields cannot be `final` → mutability risk. Cannot be tested without Spring context (no `new Service(mockDep)`). IDE cannot detect missing dependencies at startup.  
**Alternative Chosen:** Constructor injection — all dependencies passed through constructor, all injected fields `final`.  
**Retry Condition:** Never — this is a golden rule.

---

### RA-002: `SELECT *` in SQL Queries
**Date:** Platform baseline  
**Proposed By:** Developer convenience  
**Rejected By:** EEIK Architecture Guild  
**Reason:** Schema evolution (adding/removing columns) silently breaks queries. Fetches data not needed → network and memory waste. Makes query plans harder to optimise.  
**Alternative Chosen:** Always specify explicit column lists.  
**Retry Condition:** Never — this is a golden rule.

---

### RA-003: Shared Database Between Microservices
**Date:** Platform baseline  
**Proposed By:** Teams migrating from monolith  
**Rejected By:** Architecture Review  
**Reason:** Creates schema coupling — services cannot evolve or deploy independently. No clear ownership of data integrity. Scaling conflict (services compete for connections).  
**Alternative Chosen:** Each service owns its schema exclusively. Cross-context reads via API or async events.  
**Retry Condition:** Only if moving to a modular monolith (single deployment unit) — then bounded schema separation within one DB is acceptable.

---

### RA-004: CrewAI as Primary Multi-Agent Framework
**Date:** 2025-01-10  
**Proposed By:** AI Engineering team  
**Rejected By:** AI Architecture Review  
**Reason:** Role-based routing lacks explicit control flow — hard to audit routing decisions. Human-in-the-loop support limited. Less predictable under adversarial inputs in regulated domains.  
**Alternative Chosen:** LangGraph — explicit graph model, `interrupt()` for HITL, LangSmith for tracing. See ADR-G004.  
**Retry Condition:** If CrewAI adds explicit graph-based routing and LangSmith-equivalent tracing, reconsider for non-regulated domains.

---

### RA-005: `javax.*` in Spring Boot 3.x
**Date:** Platform baseline  
**Proposed By:** Legacy code migration  
**Rejected By:** EEIK Architecture Guild  
**Reason:** Spring Boot 3.x requires Jakarta EE 10. `javax.*` packages are incompatible and cause `ClassNotFoundException` at runtime.  
**Alternative Chosen:** `jakarta.*` exclusively.  
**Retry Condition:** Never — Spring Boot 3.x/4.x will not return to `javax.*`.

---

<!-- Add new rejected approaches below: -->
