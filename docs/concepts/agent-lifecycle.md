# Agent Lifecycle

## Purpose

Agents are generated assets, not permanent assets.

EEIK favors generating project-specific agents over maintaining large static agent libraries.

---

# Lifecycle Overview

```text
Blueprint
 ↓
Generation
 ↓
Validation
 ↓
Deployment
 ↓
Usage
 ↓
Learning
 ↓
Evolution
 ↓
Retirement
```

---

# Phase 1 — Blueprint

Agent blueprints define:

```yaml
purpose:
responsibilities:
inputs:
outputs:
constraints:
```

Example:

```text
Java Architect Blueprint
```

---

# Phase 2 — Generation

Agent Factory combines:

```text
Blueprint

Manifest

Capability Packs

Project Context
```

to create a project-specific agent.

---

# Phase 3 — Validation

Required Checks:

```text
Responsibility Clarity

Scope Boundaries

Output Quality

Governance Compliance
```

---

# Phase 4 — Deployment

Agent becomes available inside:

```text
.claude/
```

or

```text
GitHub Copilot
```

ecosystem.

---

# Phase 5 — Usage

Examples:

```text
Architecture Design

Code Review

Modernization Planning

Security Review
```

Metrics captured:

```text
Usage Frequency

Success Rate

Review Findings
```

---

# Phase 6 — Learning

Sources:

```text
Architecture Reviews

Delivery Outcomes

Lessons Learned

Incidents
```

Goal:

Improve future generations.

---

# Phase 7 — Evolution

Agent Factory updates:

```text
Prompts

Instructions

Constraints

Knowledge References
```

---

# Phase 8 — Retirement

Reasons:

```text
Obsolete Technology

Low Adoption

Superseded Agent
```

Example:

```text
Java 17 Architect

replaced by

Java 21 Architect
```

---

# Agent Categories

## Architecture Agents

Examples:

```text
Solution Architect

Enterprise Architect

Modernization Architect
```

---

## Engineering Agents

Examples:

```text
Java Engineer

React Engineer

Python Engineer
```

---

## Governance Agents

Examples:

```text
Security Reviewer

AI Reviewer

Production Reviewer
```

---

## Delivery Agents

Examples:

```text
Estimator

Planner

Roadmap Advisor
```

---

# Agent Quality Criteria

Every agent should be:

- Focused
- Explainable
- Governed
- Traceable
- Replaceable

---

# Guiding Principle

Agents are generated for projects.

Projects should not adapt to agents.

Agents should adapt to projects.
