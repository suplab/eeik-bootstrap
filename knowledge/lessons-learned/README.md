# Lessons Learned Repository

Captured learnings from EEIK-managed projects.

**Purpose:** Prevent teams from repeating the same mistakes. Every lesson here saved real project time.

## How to Contribute

After a sprint retrospective or project milestone, run:

```
/capture-lesson "brief description of learning"
```

The agent will guide you through the full lesson format.

## Index

| ID | Lesson | Category | Severity | Project Phase |
|----|--------|----------|----------|---------------|
| [LL-001](LL-001-flyway-baseline-on-existing-db.md) | Flyway baseline migration on existing database | Database | HIGH | Foundation |
| [LL-002](LL-002-testcontainers-startup-time.md) | Testcontainers startup slows CI pipeline | Testing | MEDIUM | Ongoing |
| [LL-003](LL-003-langgraph-recursion-limit.md) | LangGraph infinite loop without recursion_limit | AI/Agents | CRITICAL | AI Integration |
| [LL-004](LL-004-cdk-cross-account-permissions.md) | CDK cross-account deployment permission errors | AWS | HIGH | Infrastructure |

## Categories

`Database` | `Testing` | `AI/Agents` | `AWS` | `Security` | `Architecture` | `CI/CD` | `Governance`
