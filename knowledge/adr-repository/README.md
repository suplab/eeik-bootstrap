# ADR Repository

Cross-project Architecture Decision Records.

ADRs here are **cross-cutting decisions** that apply to all EEIK-managed projects. Project-specific ADRs live in `docs/decisions/` within each project repository.

## Cross-Cutting ADRs

| ID | Title | Status | Date |
|----|-------|--------|------|
| [ADR-G001](ADR-G001-hexagonal-architecture.md) | Use Hexagonal Architecture for Java services | Accepted | 2025-01-01 |
| [ADR-G002](ADR-G002-outbox-pattern.md) | Use Outbox Pattern for transactional event publishing | Accepted | 2025-01-01 |
| [ADR-G003](ADR-G003-cdk-typescript.md) | Use CDK TypeScript for all AWS infrastructure | Accepted | 2025-01-01 |
| [ADR-G004](ADR-G004-langgraph-multi-agent.md) | Use LangGraph for multi-agent AI systems | Accepted | 2025-01-15 |
| [ADR-G005](ADR-G005-rfc7807-errors.md) | Use RFC 7807 ProblemDetail for REST error responses | Accepted | 2025-01-01 |
| [ADR-G006](ADR-G006-constructor-injection.md) | Require constructor injection — no field injection | Accepted | 2025-01-01 |

## Format

Every ADR follows the template in `capability-packs/architecture/templates/adr-template.md`.

## Lifecycle

```
Proposed → Accepted → Deprecated → Superseded
```

Once Accepted, an ADR is immutable. Changing a decision requires a new ADR that supersedes it.
