# Canonical Repository Layout Reference

## Purpose

This template defines the canonical structure for each project type combination.
The Repository Generator uses this as the master reference.

---

## Java Spring Boot — Microservice (DDD + Hexagonal)

```
{service-name}/
├── src/
│   ├── main/
│   │   ├── java/com/{org}/{service}/
│   │   │   ├── domain/
│   │   │   │   ├── model/
│   │   │   │   │   ├── {Aggregate}.java
│   │   │   │   │   └── {ValueObject}.java
│   │   │   │   ├── repository/
│   │   │   │   │   └── {Aggregate}Repository.java
│   │   │   │   ├── service/
│   │   │   │   │   └── {Domain}Service.java
│   │   │   │   └── event/
│   │   │   │       └── {Domain}Event.java
│   │   │   ├── application/
│   │   │   │   ├── port/
│   │   │   │   │   ├── in/
│   │   │   │   │   │   └── {UseCase}UseCase.java
│   │   │   │   │   └── out/
│   │   │   │   │       └── {Resource}Port.java
│   │   │   │   ├── usecase/
│   │   │   │   │   └── {UseCase}Service.java
│   │   │   │   └── dto/
│   │   │   │       └── {UseCase}Request.java
│   │   │   ├── infrastructure/
│   │   │   │   ├── persistence/
│   │   │   │   │   ├── {Aggregate}JpaEntity.java
│   │   │   │   │   └── {Aggregate}JpaRepository.java
│   │   │   │   ├── messaging/
│   │   │   │   │   ├── {Event}Publisher.java
│   │   │   │   │   └── {Event}Consumer.java
│   │   │   │   └── config/
│   │   │   │       └── {Service}Config.java
│   │   │   └── web/
│   │   │       ├── controller/
│   │   │       │   └── {Resource}Controller.java
│   │   │       ├── request/
│   │   │       │   └── {Resource}Request.java
│   │   │       └── response/
│   │   │           └── {Resource}Response.java
│   │   └── resources/
│   │       ├── application.yaml
│   │       ├── application-local.yaml
│   │       └── db/migration/
│   │           └── V1__initial_schema.sql
│   └── test/
│       ├── java/com/{org}/{service}/
│       │   ├── domain/        ← Pure unit tests
│       │   ├── application/   ← Use case unit tests (mocked ports)
│       │   ├── infrastructure/← Integration tests (Testcontainers)
│       │   ├── web/           ← @WebMvcTest slices
│       │   └── contract/      ← Pact consumer/provider tests
│       └── resources/
│           └── application-test.yaml
│
├── infrastructure/
│   └── cdk/
│       ├── bin/app.ts
│       ├── lib/
│       │   ├── network-stack.ts
│       │   ├── database-stack.ts
│       │   ├── application-stack.ts
│       │   └── observability-stack.ts
│       ├── package.json
│       └── tsconfig.json
│
├── .github/
│   ├── copilot-instructions.md
│   └── workflows/
│       ├── build.yaml
│       ├── deploy-dev.yaml
│       └── deploy-prod.yaml
│
├── .claude/
│   ├── agents/
│   ├── commands/
│   ├── memory/
│   │   ├── project-context.md
│   │   ├── domain-glossary.md
│   │   ├── decisions.md
│   │   ├── constraints.md
│   │   └── patterns.md
│   └── standards/
│
├── docs/
│   ├── architecture/
│   │   └── solution-architecture.md
│   └── decisions/
│       └── ADR-0001-architecture-style.md
│
├── pom.xml
├── CLAUDE.md
└── README.md
```

---

## Python FastAPI — AI Agent Service

```
{service-name}/
├── src/
│   └── {service}/
│       ├── __init__.py
│       ├── main.py                     ← FastAPI app factory
│       ├── domain/
│       │   ├── models/
│       │   │   └── {entity}.py         ← Pydantic domain models
│       │   ├── repositories/
│       │   │   └── {entity}_repo.py    ← Repository interface (ABC)
│       │   └── events/
│       │       └── {domain}_event.py
│       ├── application/
│       │   ├── use_cases/
│       │   │   └── {use_case}.py
│       │   └── dtos/
│       │       └── {resource}_dto.py
│       ├── infrastructure/
│       │   ├── persistence/
│       │   │   └── {entity}_repo_impl.py
│       │   ├── messaging/
│       │   │   └── {event}_publisher.py
│       │   └── config/
│       │       └── settings.py         ← pydantic-settings BaseSettings
│       ├── api/
│       │   ├── routes/
│       │   │   └── {resource}_router.py
│       │   ├── dependencies/
│       │   │   └── {resource}_deps.py
│       │   └── middleware/
│       │       └── logging_middleware.py
│       └── agents/
│           ├── state/
│           │   └── {agent}_state.py
│           ├── nodes/
│           │   └── {node}_node.py
│           ├── graphs/
│           │   └── {agent}_graph.py
│           ├── tools/
│           │   └── {tool}_tool.py
│           └── memory/
│               └── memory_strategy.py
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── contract/
│
├── infrastructure/cdk/
│
├── .github/workflows/
├── .claude/
├── docs/
│
├── pyproject.toml
├── requirements.txt
├── CLAUDE.md
└── README.md
```

---

## Layout Rules

1. **Source code never goes in the root** — always under `src/`
2. **Infrastructure is always isolated** — `infrastructure/` at root level
3. **`.claude/` is always present** — minimum: memory/, agents/, commands/
4. **`docs/` is always present** — minimum: architecture/, decisions/
5. **Test mirrors production** — test package structure matches main
6. **One CDK stack per concern** — network, database, application, observability
7. **CI/CD per environment** — separate workflow file per deployment target
