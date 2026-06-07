# Capability Lifecycle

## Purpose

Capability Packs are the fundamental unit of reuse within EEIK.

A Capability Pack encapsulates reusable engineering intelligence including:

- Standards
- Templates
- Agents
- Workflows
- Commands
- Governance Rules
- Knowledge Assets

This document defines the lifecycle of a Capability Pack.

---

# Lifecycle Overview

```text
Idea
 ↓
Proposal
 ↓
Design
 ↓
Implementation
 ↓
Review
 ↓
Approval
 ↓
Publication
 ↓
Usage
 ↓
Evolution
 ↓
Retirement
```

---

# Phase 1 — Idea

An engineering team identifies reusable knowledge.

Examples:

```text
Java 21 Standards

AWS CDK Standards

React Standards

IBM i Modernization

AI Engineering
```

Goal:

Determine whether the capability has organization-wide value.

---

# Phase 2 — Proposal

Create a capability proposal.

Required Information:

```yaml
name:
owner:
domain:
problem_statement:
business_value:
target_audience:
```

---

# Phase 3 — Design

Define capability structure.

Typical Components:

```text
agents/
commands/
workflows/
templates/
standards/
governance/
knowledge/
```

Deliverables:

```text
Capability Design Document

Initial Scope

Dependencies
```

---

# Phase 4 — Implementation

Build capability assets.

Examples:

```text
Instructions

Templates

Prompts

Commands

Review Checklists

Workflows
```

---

# Phase 5 — Review

Reviews Required:

## Architecture Review

Validate technical quality.

---

## Governance Review

Validate compliance requirements.

---

## AI Review

Required when agents are included.

---

# Phase 6 — Approval

Capability becomes approved for enterprise use.

Approval Criteria:

- Documentation Complete
- Governance Complete
- Standards Defined
- Review Passed

---

# Phase 7 — Publication

Capability Pack is published.

Example:

```text
capability-packs/

java-pack/

aws-pack/

governance-pack/
```

---

# Phase 8 — Usage

Capability is consumed by projects.

Example:

```yaml
selected-packs:

  - java-pack

  - aws-pack

  - governance-pack
```

---

# Phase 9 — Evolution

Capability continuously improves.

Sources:

- Lessons Learned
- Architecture Reviews
- Incidents
- Modernization Programs

---

# Phase 10 — Retirement

Retirement occurs when:

- Technology is obsolete
- Capability is superseded
- Organization no longer requires it

Example:

```text
java17-pack

replaced by

java21-pack
```

---

# Versioning

Recommended Versioning:

```text
Major.Minor.Patch
```

Example:

```text
2.3.1
```

---

# Ownership

Every capability must define:

```yaml
owner:
maintainers:
reviewers:
```

Capabilities without ownership should not be published.

---

# Success Metrics

Measure:

- Adoption
- Reuse
- Quality
- Review Findings
- Governance Compliance

---

# Guiding Principle

Capabilities are products.

They should be maintained with the same rigor as production software.
