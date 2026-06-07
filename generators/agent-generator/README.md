# Agent Generator — Agent Factory

## Purpose

The Agent Factory generates project-specific agents from reusable blueprints.

Instead of maintaining hundreds of static agents, EEIK stores a small set of archetypal blueprints and composes project-specific agents at generation time.

## Design Principle

```
Static catalog:              Generated agents:
200 hardcoded agents    →    20 blueprints
                             × manifest context
                             = ∞ specialised agents
```

## Primary Command

```text
/generate-agent
```

## Invocation

```text
/generate-agent --name "claims-triage-specialist" \
                --category specialist \
                --domain insurance \
                --from blueprint: specialist
```

Or as part of `/generate-repo` — agents are auto-generated based on manifest.

## Blueprints Available

| Blueprint    | Purpose                                       | Generates |
|--------------|-----------------------------------------------|-----------|
| architect    | Technical design and system thinking          | architect, solution-architect, domain-architect |
| engineer     | Hands-on implementation                       | java-developer, python-engineer, frontend-dev |
| reviewer     | Code and architecture review                  | code-reviewer, java-reviewer, api-reviewer |
| auditor      | Security, compliance, governance checks       | security-auditor, compliance-reviewer |
| planner      | Estimation, scheduling, delivery              | estimator, project-tracker, sprint-planner |
| specialist   | Domain or technology specialist               | langgraph-engineer, rag-specialist, ibmi-expert |
| investigator | RCA, incident analysis, debugging             | rca-agent, incident-handler |
| coordinator  | Multi-agent orchestration and routing         | supervisor-agent, router-agent |

## Generated Assets

For each agent, the factory produces:

```
.claude/agents/{agent-name}.md          ← Agent definition (Claude Code frontmatter)
```

Optionally also:

```
.claude/prompts/{agent-name}-system.md  ← Expanded system prompt
```

## Structure

```
agent-generator/
│
├── README.md
│
├── blueprints/
│   ├── architect.yaml
│   ├── engineer.yaml
│   ├── reviewer.yaml
│   ├── auditor.yaml
│   ├── planner.yaml
│   ├── specialist.yaml
│   ├── investigator.yaml
│   └── coordinator.yaml
│
├── prompts/
│   ├── generate-agent.md      ← Master generation prompt
│   ├── generate-memory.md     ← Memory strategy generation
│   └── generate-evaluation.md ← Evaluation dataset generation
│
├── templates/
│   ├── agent-template.md      ← Output agent.md template
│   └── evaluation-template.md ← Output evaluation dataset template
│
└── workflows/
    └── agent-generation.yaml
```
