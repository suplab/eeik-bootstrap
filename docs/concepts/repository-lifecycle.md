# Repository Lifecycle

## Purpose

EEIK repositories should follow a standardized lifecycle.

The goal is to ensure consistency, governance, and maintainability across projects.

---

# Lifecycle Overview

```text
Bootstrap
 ↓
Planning
 ↓
Generation
 ↓
Development
 ↓
Governance
 ↓
Operations
 ↓
Knowledge Capture
 ↓
Retirement
```

---

# Phase 1 — Bootstrap

Command:

```text
/bootstrap
```

Outputs:

```text
project-manifest.yaml

selected-capabilities.yaml

recommended-agents.yaml

repository-plan.md
```

Goal:

Understand project requirements.

---

# Phase 2 — Planning

Generated Artifacts:

```text
Architecture

Roadmap

Governance Profile

Delivery Plan
```

Goal:

Define target state.

---

# Phase 3 — Generation

Command:

```text
/generate-repository
```

Generated Structure:

```text
.github/
.claude/
src/
docs/
infrastructure/
```

Goal:

Create a standardized project workspace.

---

# Phase 4 — Development

Activities:

```text
Architecture

Implementation

Testing

Automation
```

Standards:

Inherited from selected capability packs.

---

# Phase 5 — Governance

Required Reviews:

```text
Architecture Review

Security Review

AI Review

Production Readiness Review
```

Goal:

Reduce risk before deployment.

---

# Phase 6 — Operations

Activities:

```text
Monitoring

Observability

Incident Management

Release Management
```

Goal:

Maintain operational excellence.

---

# Phase 7 — Knowledge Capture

Artifacts:

```text
ADRs

Lessons Learned

Postmortems

Reference Architectures
```

Goal:

Preserve organizational intelligence.

---

# Phase 8 — Retirement

Activities:

```text
Archive Repository

Capture Knowledge

Retire Dependencies
```

Goal:

Ensure orderly decommissioning.

---

# Repository States

## Planned

Project not yet generated.

---

## Active

Repository under active development.

---

## Governed

Repository has passed governance controls.

---

## Operational

Repository supports production workloads.

---

## Retired

Repository no longer maintained.

---

# Success Metrics

- Time to Bootstrap
- Time to First Commit
- Governance Coverage
- Documentation Completeness
- Knowledge Capture Rate

---

# Guiding Principle

Repositories are temporary.

Knowledge and capabilities are permanent.
