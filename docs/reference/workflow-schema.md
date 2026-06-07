# Workflow Schema

## Purpose

Defines a standard structure for EEIK workflows.

Workflows orchestrate engineering activities.

---

# Workflow Structure

```yaml
name:

version:

purpose:

inputs:

steps:

outputs:

governance:
```

---

# Example

```yaml
name: architecture-review

version: 1.0

purpose: review architecture

inputs:

  - architecture-document

steps:

  - validate

  - analyze

  - review

  - report

outputs:

  - review-report

governance:

  - architecture-review
```

---

# Required Fields

```yaml
name

purpose

steps

outputs
```

---

# Workflow Step Structure

```yaml
step:

  id:

  description:

  inputs:

  outputs:
```

---

# Workflow Categories

```text
Architecture

Engineering

Governance

Modernization

Delivery

Knowledge
```

---

# Validation Rules

- Unique Workflow Name
- At Least One Step
- At Least One Output
- Valid Governance Mapping

---

# Execution Principles

1. Deterministic
2. Traceable
3. Auditable
4. Reusable
