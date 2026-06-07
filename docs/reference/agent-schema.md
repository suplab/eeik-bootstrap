# Agent Schema

## Purpose

Defines the structure of generated and blueprint agents.

Agents are generated assets created by the Agent Factory.

---

# Agent Structure

```yaml
name:

version:

category:

purpose:

responsibilities:

inputs:

outputs:

constraints:

memory:

governance:
```

---

# Example

```yaml
name: java-solution-architect

version: 1.0

category: architecture

purpose:

  design java solutions

responsibilities:

  - design

  - review

  - recommend

inputs:

  - requirements

  - architecture

outputs:

  - recommendations

constraints:

  - no implementation ownership

memory:

  - standards

  - architecture-patterns

governance:

  - architecture-review
```

---

# Required Fields

```yaml
name

category

purpose

responsibilities
```

---

# Agent Categories

```text
Architecture

Engineering

Governance

Delivery

Operations

AI

Modernization
```

---

# Governance Requirements

Every agent must define:

```yaml
constraints

escalation_rules

review_requirements
```

---

# Quality Requirements

Agents should be:

- Explainable
- Traceable
- Governed
- Focused
- Replaceable

---

# Lifecycle States

```text
Blueprint

Generated

Validated

Active

Deprecated

Retired
```

---

# Validation Rules

- Unique Name
- Valid Category
- Responsibilities Defined
- Governance Defined
