# ADR-G001: Use Hexagonal Architecture for Java Services

**Status:** Accepted  
**Date:** 2025-01-01  
**Author:** EEIK Architecture Guild  
**Applies To:** All Java Spring Boot microservices

---

## Context

Java Spring Boot services historically mix domain logic, persistence, and web concerns. This leads to:
- Business logic in `@RestController` methods
- Domain entities with JPA annotations (infrastructure leaking into domain)
- Services that are impossible to test without a database
- High coupling between layers

## Decision

All Java microservices will use the **Hexagonal Architecture** (Ports and Adapters) with this package structure:

```
domain/          ← Pure domain: entities, value objects, domain events, domain services
application/     ← Use cases (ports in/out), DTOs, orchestration
infrastructure/  ← Adapters: JPA, messaging, external clients
web/             ← HTTP adapter: controllers, request/response DTOs
```

**Constraints:**
- Domain classes have zero Spring annotations (no `@Entity`, `@Service` in `domain/`)
- `@Entity` lives in `infrastructure/persistence/` — JPA entities are infrastructure adapters
- Application layer depends only on domain — never on infrastructure
- Dependencies flow inward: web → application → domain

## Rationale

- Domain logic is testable with pure unit tests — no Spring context required
- Infrastructure can be swapped without touching domain (e.g. DB2 → Aurora)
- Clear ownership of business rules
- Aligns with DDD bounded contexts

## Consequences

**Positive:**
- Test pyramid achievable — most tests are fast unit tests
- Clear code navigation by reading package names
- Infrastructure changes isolated to `infrastructure/`

**Negative:**
- More classes than flat service pattern
- Mapping boilerplate between JPA entities and domain models
- Steeper learning curve for developers new to hexagonal architecture

## Alternatives Considered

| Alternative | Reason Rejected |
|-------------|----------------|
| Flat `service/`, `repository/`, `controller/` packages | No domain/infrastructure separation |
| CQRS from day 1 | Over-engineered for most services; can be layered on top of hexagonal later |
| Spring's default layered architecture | Creates coupling between layers |

**Review Trigger:** If more than 3 services find the JPA entity/domain model mapping excessive, review whether Spring Data JDBC would reduce boilerplate while maintaining separation.
