# Command Schema

## Purpose

Commands are user-facing entry points into workflows.

Commands simplify access to platform capabilities.

---

# Command Structure

```yaml
name:

description:

workflow:

inputs:

outputs:
```

---

# Example

```yaml
name: bootstrap

description: initialize project

workflow:

  bootstrap-workflow

inputs:

  project-context

outputs:

  project-manifest
```

---

# Naming Convention

```text
verb-noun
```

Examples:

```text
bootstrap

create-architecture

generate-agents

review-security

capture-knowledge
```

---

# Required Fields

```yaml
name

description

workflow
```

---

# Optional Fields

```yaml
inputs

outputs

examples
```

---

# Command Categories

```text
Bootstrap

Architecture

Engineering

Governance

Knowledge

Modernization

Delivery
```

---

# Validation Rules

- Unique Name
- Valid Workflow Reference
- Valid Inputs
- Valid Outputs

---

# Design Principles

1. Discoverable
2. Consistent
3. Simple
4. Traceable
