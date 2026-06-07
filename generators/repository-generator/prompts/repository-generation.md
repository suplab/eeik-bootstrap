# Repository Generation Prompt

## Purpose

Generate a complete repository scaffold from a validated `project-manifest.yaml`.

This prompt is activated by the `/generate-repo` command.

---

## Pre-Conditions

Before executing this prompt, verify:

1. `project-manifest.yaml` exists in the project root
2. `/validate-manifest` has been run and returned PASS
3. Required capability packs are available

If any condition fails, halt and report the blocker.

---

## Step 1 — Read the Manifest

Read `project-manifest.yaml` and extract:

```yaml
project.name
project.project_type
project.domain
technology.backend.language
technology.backend.framework
technology.backend.version
technology.frontend.framework
technology.database.type
technology.database.migration_tool
technology.messaging.broker
technology.messaging.pattern
architecture.style
architecture.patterns[]
cloud.provider
cloud.infra_as_code
cloud.regions[]
cloud.multi_account
ai.enabled
ai.pattern
ai.framework
ai.memory_pattern
governance.profile
governance.reviews_required[]
governance.compliance_frameworks[]
delivery.model
delivery.cicd_platform
observability.enabled
observability.slo_required
```

---

## Step 2 — Resolve Capability Packs

Based on the manifest values, identify which capability packs apply.

Use the resolution rules:

| Manifest Value                  | Capability Pack Required          |
|---------------------------------|-----------------------------------|
| backend.language = java         | java-pack                         |
| backend.language = python       | python-pack (planned)             |
| cloud.provider = aws            | aws-pack                          |
| ai.enabled = true               | ai-engineering-pack               |
| governance.profile = regulated  | governance-pack (full review set) |
| governance.profile = enterprise | governance-pack (full review set) |
| architecture.style = any        | architecture-pack (always)        |

Read the relevant pack metadata from `capability-packs/{pack}/metadata.yaml` to understand what agents, standards, and templates are available.

---

## Step 3 — Generate Directory Structure

Output the complete directory tree for the project.

### Java Spring Boot Microservice Structure

When `technology.backend.language = java` and `technology.backend.framework = spring-boot`:

```
{project.name}/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── {organisation}/
│   │   │           └── {project}/
│   │   │               ├── domain/
│   │   │               │   ├── model/           ← Entities, Value Objects, Aggregates
│   │   │               │   ├── repository/      ← Repository interfaces (domain layer)
│   │   │               │   ├── service/         ← Domain services
│   │   │               │   └── event/           ← Domain events
│   │   │               ├── application/
│   │   │               │   ├── port/
│   │   │               │   │   ├── in/          ← Use case interfaces
│   │   │               │   │   └── out/         ← Repository/gateway interfaces
│   │   │               │   ├── usecase/         ← Use case implementations
│   │   │               │   └── dto/             ← Application DTOs
│   │   │               ├── infrastructure/
│   │   │               │   ├── persistence/     ← JPA entities, repositories
│   │   │               │   ├── messaging/       ← Event publishers/consumers
│   │   │               │   ├── client/          ← External service clients
│   │   │               │   └── config/          ← Spring configuration classes
│   │   │               └── web/
│   │   │                   ├── controller/      ← REST controllers
│   │   │                   ├── request/         ← Request DTOs
│   │   │                   └── response/        ← Response DTOs
│   │   └── resources/
│   │       ├── application.yaml
│   │       ├── application-dev.yaml
│   │       ├── application-staging.yaml
│   │       └── db/
│   │           └── migration/                   ← Flyway migration scripts
│   └── test/
│       ├── java/
│       │   └── com/{organisation}/{project}/
│       │       ├── domain/                      ← Unit tests (domain logic)
│       │       ├── application/                 ← Unit tests (use cases)
│       │       ├── infrastructure/              ← Integration tests (Testcontainers)
│       │       ├── web/                         ← Controller slice tests
│       │       └── contract/                    ← Pact contract tests
│       └── resources/
│           └── application-test.yaml
│
├── infrastructure/
│   └── cdk/                                     ← CDK stacks (TypeScript)
│       ├── bin/
│       │   └── app.ts
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
│       ├── deploy-staging.yaml
│       └── deploy-prod.yaml
│
├── .claude/
│   ├── agents/                                  ← Project-specific generated agents
│   ├── commands/                                ← Project-specific commands
│   ├── memory/
│   │   ├── project-context.md
│   │   ├── domain-glossary.md
│   │   ├── decisions.md
│   │   ├── constraints.md
│   │   └── patterns.md
│   └── standards/
│       └── (symlinks or copies from capability packs)
│
├── docs/
│   ├── architecture/
│   │   └── solution-architecture.md             ← From architecture template
│   └── decisions/                               ← ADR directory
│
├── pom.xml                                      ← Maven build (Java)
├── CLAUDE.md                                    ← Project CLAUDE.md (from manifest)
└── README.md
```

### Python FastAPI Structure

When `technology.backend.language = python` and `technology.backend.framework = fastapi`:

```
{project.name}/
│
├── src/
│   └── {project}/
│       ├── domain/
│       │   ├── models/          ← Pydantic domain models
│       │   ├── repositories/    ← Repository interfaces
│       │   └── events/          ← Domain events
│       ├── application/
│       │   ├── use_cases/       ← Application use cases
│       │   └── dtos/            ← Request/response DTOs
│       ├── infrastructure/
│       │   ├── persistence/     ← Database adapters
│       │   ├── messaging/       ← Event publishers/consumers
│       │   └── clients/         ← External service clients
│       └── api/
│           ├── routes/          ← FastAPI routers
│           ├── dependencies/    ← FastAPI dependency injection
│           └── middleware/      ← Middleware
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── contract/
│
├── infrastructure/cdk/          ← CDK stacks
│
├── .github/workflows/
├── .claude/
├── docs/
│
├── pyproject.toml
├── requirements.txt
└── CLAUDE.md
```

---

## Step 4 — Apply Architecture Pattern Overlays

For each pattern in `architecture.patterns`, apply the overlay:

### DDD Overlay

Ensure the package structure has:
- `domain/model/` — Entities, Value Objects, Aggregates
- `domain/event/` — Domain Events
- `application/port/in/` — Primary ports (use cases)
- `application/port/out/` — Secondary ports (repositories, gateways)
- `infrastructure/` — Adapters implementing secondary ports

Generate stub files:
- `{AggregateRoot}.java` — with `@Entity`, `@AggregateRoot` comment, private fields, factory method
- `{DomainEvent}.java` — record with `occurredAt: Instant`
- `{UseCaseName}UseCase.java` — interface with single `execute()` method
- `{UseCaseName}Service.java` — implementation with constructor injection

### CQRS Overlay

Split into:
- `application/command/` — Command objects + handlers
- `application/query/` — Query objects + handlers
- `infrastructure/command/` — Command-side repositories
- `infrastructure/query/` — Query-side read models

### Outbox Pattern Overlay

Add to `infrastructure/persistence/`:
- `OutboxEvent.java` — entity with `status`, `payload`, `occurredAt`
- `OutboxRepository.java` — interface with `findPending()` method
- `OutboxEventPublisher.java` — scheduled publisher class stub

Add Flyway migration:
- `V{n}__create_outbox_table.sql`

### Saga Overlay

Add `application/saga/`:
- `{ProcessName}Saga.java` — saga orchestrator stub
- `{ProcessName}SagaState.java` — saga state machine

### API Gateway Overlay

Add infrastructure:
- CDK: `ApiGatewayStack.ts` with REST API, usage plan, WAF association
- Route configuration stub

---

## Step 5 — Generate AI/Agentic Overlay

When `ai.enabled = true`:

### LangGraph Overlay (framework = langgraph)

Add to project:

```
src/
└── agents/
    ├── state/
    │   └── {project_name}_state.py     ← TypedDict state schema
    ├── nodes/
    │   ├── __init__.py
    │   └── {node_name}_node.py         ← Individual node stubs
    ├── graphs/
    │   └── {project_name}_graph.py     ← Graph assembly + compile
    ├── tools/
    │   └── {tool_name}_tool.py         ← Tool stubs
    └── memory/
        └── memory_strategy.py          ← Memory implementation stub
```

Generate stub content for `{project_name}_graph.py`:

```python
"""
{project.name} — LangGraph Agent Graph
Generated by: EEIK Repository Generator
Pattern: {ai.pattern}
Memory: {ai.memory_pattern}
"""
from typing import Annotated
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.memory import MemorySaver

class {ProjectName}State(TypedDict):
    messages: Annotated[list, "conversation messages"]
    context: Annotated[dict, "retrieved context"]
    # TODO: Add project-specific state fields

def build_graph() -> StateGraph:
    graph = StateGraph({ProjectName}State)
    # TODO: Add nodes
    # graph.add_node("node_name", node_function)
    # TODO: Add edges
    # graph.set_entry_point("node_name")
    return graph.compile(checkpointer=MemorySaver())

graph = build_graph()
```

### Memory Overlay

Based on `ai.memory_pattern`:

| Pattern       | Generated Stub                                      |
|---------------|-----------------------------------------------------|
| in-session    | `MemorySaver` import only                           |
| persistent-db | `PostgresSaver` with DSN config stub                |
| vector-store  | `vector_store.py` with embedding + retrieval stubs  |

---

## Step 6 — Generate Infrastructure

### CDK (aws + cdk)

Generate `infrastructure/cdk/lib/`:

**`network-stack.ts`** — VPC with private/isolated subnets, NAT gateway, VPC Flow Logs

**`database-stack.ts`** — Aurora cluster (if aurora-postgresql) or DynamoDB table (if dynamodb), secret rotation

**`application-stack.ts`** — ECS Fargate service, task definition, ALB, auto-scaling

**`observability-stack.ts`** — CloudWatch dashboards, alarms, X-Ray tracing group

Include:
- `cdk.context.json` with account/region placeholders
- `cdk.json` with app entry point
- Tags applied via `Tags.of(this).add()`
- SSM exports for cross-stack references

### CI/CD (cicd_platform = github-actions)

Generate `.github/workflows/`:

**`build.yaml`** — Triggered on PR to main:
- Checkout + setup JDK / Python
- Build + test
- Coverage check (fail < 80%)
- Security scan (OWASP Dependency Check)
- `cdk synth` (validate infra)
- Docker build (no push)

**`deploy-dev.yaml`** — Triggered on merge to main:
- Build + test
- `cdk deploy --require-approval=never` to dev account
- Integration test smoke check

**`deploy-prod.yaml`** — Manual trigger with environment protection:
- Requires 2 approvals
- `/deploy-check` gates
- Blue/green deployment
- Post-deploy health check

---

## Step 7 — Generate Governance Assets

Based on `governance.profile`:

| Profile    | Generated Artifacts                                                            |
|------------|--------------------------------------------------------------------------------|
| basic      | `docs/decisions/` (empty), `CLAUDE.md`                                         |
| standard   | + `docs/architecture/solution-architecture.md`, review checklist               |
| regulated  | + `docs/risk-register.md`, compliance mapping, security baseline checklist     |
| enterprise | + ARB submission template, `docs/compliance/` folder, SLO definition template  |

For each `compliance_framework`:

| Framework | Generated                                           |
|-----------|-----------------------------------------------------|
| gdpr      | `docs/compliance/gdpr-mapping.md` template          |
| pci-dss   | `docs/compliance/pci-dss-mapping.md` template       |
| hipaa     | `docs/compliance/hipaa-mapping.md` template         |

---

## Step 8 — Generate .claude/ Configuration

### CLAUDE.md

Generate project-specific `CLAUDE.md` with:
- Project name and description from manifest
- Technology stack summary
- Active golden rules (from relevant capability packs)
- Available agents (generated by agent-generator)
- Slash commands available
- Memory file locations

### agents/

Generate project-specific agents (call agent-generator):
- Always: `architect`, `code-reviewer`
- If java: `java-developer`, `java-tech-lead`, `java-tester`
- If aws: `aws-architect`, `cdk-engineer`
- If ai.enabled: `ai-architect`, appropriate framework engineer
- If governance = regulated/enterprise: `security-auditor`, `compliance-reviewer`

### memory/project-context.md

Pre-populate from manifest:
```markdown
# Project Context

**Name:** {project.name}
**Domain:** {project.domain}
**Type:** {project.project_type}
**Owner:** {project.owner}

## Technology
- Backend: {technology.backend.language} {technology.backend.version} / {technology.backend.framework}
- Database: {technology.database.type} (migrations: {technology.database.migration_tool})
- Cloud: {cloud.provider} / {cloud.infra_as_code}
- Regions: {cloud.regions}

## Architecture
- Style: {architecture.style}
- Patterns: {architecture.patterns}

## Governance
- Profile: {governance.profile}
- Reviews required: {governance.reviews_required}

## AI
- Enabled: {ai.enabled}
- Pattern: {ai.pattern}
- Framework: {ai.framework}
```

---

## Step 9 — Output Generation Report

After scaffold is complete, output:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  EEIK Repository Generator — Complete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Project:         {project.name}
Type:            {project.project_type}
Domain:          {project.domain}

Generated:
  ✅ Directory structure ({n} directories)
  ✅ Source skeleton ({n} files)
  ✅ Infrastructure stubs ({n} CDK stacks)
  ✅ CI/CD workflows ({n} pipeline files)
  ✅ .claude/ configuration ({n} agents)
  ✅ .github/ configuration
  ✅ Governance artifacts ({n} documents)

Capability Packs Applied:
  {pack-list with ✅/⚠️}

Patterns Applied:
  {pattern-list}

Next Steps:
  1. Review generated CLAUDE.md and customise
  2. Replace all TODO placeholders in skeleton files
  3. Set real values in cdk.context.json
  4. Run /validate-manifest to confirm alignment
  5. Push to feature branch and open PR
  6. Trigger architecture-review (if governance requires)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Error Handling

| Condition                            | Response                                          |
|--------------------------------------|---------------------------------------------------|
| Manifest missing                     | HALT — run `/bootstrap` first                     |
| Manifest invalid                     | HALT — run `/validate-manifest` first             |
| Capability pack not built (📋)       | WARN — skip pack, note gap in report              |
| AI enabled but framework missing     | WARN — skip AI overlay, flag for resolution       |
| Regulated domain + basic governance  | ERROR — blocked by validate-manifest              |
