# Project Constraints

> Hard constraints that must NEVER be violated. These are non-negotiable limits.
> 
> **Bootstrap Template** — Add your project-specific constraints below the EEIK defaults.

---

## EEIK Platform Constraints (Apply to All Projects)

| Constraint | Reason | Implication |
|------------|--------|-------------|
| Constructor injection only — no `@Autowired` on fields | Testability and immutability | All Spring beans accept dependencies via constructor; fields are `final` |
| `jakarta.*` only — no `javax.*` | Spring Boot 3.x / Jakarta EE 10 | Any legacy code using `javax.*` must be migrated before integration |
| No hardcoded secrets or credentials anywhere in source | Security baseline | All secrets via AWS Secrets Manager or environment variables |
| No `SELECT *` in any SQL | Performance and schema evolution safety | Always list columns explicitly |
| Parameterised queries only — no SQL string concatenation | SQL injection prevention | Use `NamedParameterJdbcTemplate` or JPQL named parameters |
| SLF4J with parameterised messages — no `System.out.println()` | Structured logging | `log.info("Created order id={}", orderId)` |
| `java.time` only — no `Date` or `Calendar` | Thread safety and clarity | `LocalDate`, `LocalDateTime`, `Instant`, `ZonedDateTime` |
| Conventional Commits format | Automated changelog and semantic versioning | `feat(scope): description`, `fix(scope): description` |
| No partial implementations in committed code | Code quality | Every method body is complete; no `// TODO implement this` |
| No direct commits to `main` or `master` | Code review gate | Always feature branch + PR + at least one approval |
| No `Thread.sleep()` in tests | Flaky tests | Use `Awaitility.await().until(...)` |
| No empty catch blocks | Silent failure prevention | At minimum `log.warn("...", e)` |

---

## Technical Constraints

> Replace with your project's actual constraints after `/bootstrap`

| Constraint | Reason | Implication |
|------------|--------|-------------|
| Java 21 minimum | LTS support | No features removed in 21 |
| AWS eu-west-1 primary region | Data residency | All production data must remain in eu-west-1 |
| <!-- TODO: Add project-specific constraints --> | | |

---

## Business / Regulatory Constraints

> Add if applicable to your domain

| Constraint | Framework | Implication |
|------------|-----------|-------------|
| Personal data must not be logged in plaintext | GDPR Article 32 | Mask PII in all log statements |
| Card data must never be stored | PCI-DSS Requirement 3 | Tokenise at point of capture; never persist PAN |
| AI decisions affecting individuals must be explainable | EU AI Act / GDPR Article 22 | Log model inputs, outputs, and reasoning for all consequential decisions |
| <!-- TODO: Add regulatory constraints for your domain --> | | |

---

## Architecture Constraints

| Constraint | Reason |
|------------|--------|
| No cross-context database joins | DDD bounded context integrity |
| No service-to-service synchronous calls in the critical path exceeding 3 hops | Latency and cascading failure risk |
| All external integrations must have circuit breakers | Resilience |
| CDK stacks must be deployable independently | Safe incremental deployment |
