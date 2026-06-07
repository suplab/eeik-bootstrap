# Model Routing

## Purpose

Different workloads require different AI capabilities.

EEIK routes work to the most appropriate model.

---

# Objectives

Optimize:

- Quality
- Cost
- Context Length
- Reliability
- Latency

---

# Routing Philosophy

Do not use the same model for all tasks.

Match model strengths to workload.

---

# Workload Categories

## Architecture

Examples:

- Solution Design
- Modernization Planning
- Enterprise Architecture

Preferred:

```text
Claude Sonnet
```

Fallback:

```text
GPT
```

---

## Software Engineering

Examples:

- Java
- Spring Boot
- React
- Angular
- Python

Preferred:

```text
Claude Sonnet
```

Fallback:

```text
GPT
```

---

## Governance

Examples:

- Security Review
- Architecture Review
- AI Review

Preferred:

```text
Claude Sonnet
```

---

## Documentation

Examples:

- README
- ADR
- RFC

Preferred:

```text
GPT
Claude Sonnet
```

---

## Agent Design

Examples:

- Agent Generation
- Prompt Generation
- Workflow Design

Preferred:

```text
Claude Sonnet
```

---

## Low Complexity

Examples:

- Formatting
- Refactoring
- Template Expansion

Preferred:

```text
Local Models
```

---

# Routing Metadata

Each request records:

```yaml
task_type:
complexity:
selected_model:
fallback_model:
```

---

# Future Direction

Future releases may support:

- Dynamic Routing
- Multi-Model Collaboration
- Cost-Aware Routing
- Quality Scoring
- Automatic Failover
