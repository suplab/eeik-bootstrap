# Capability Pack Schema

## Purpose

Defines the structure of a Capability Pack.

Capability Packs are reusable units of engineering intelligence.

---

# Capability Structure

```yaml
name:

version:

owner:

description:

dependencies:

agents:

commands:

workflows:

templates:

standards:

governance:
```

---

# Example

```yaml
name: java-pack

version: 1.0

owner: platform-engineering

description: Java engineering standards

dependencies:

  - architecture-pack

agents:

  - java-architect

  - java-engineer

commands:

  - review-java

workflows:

  - java-design-review

templates:

  - service-template

standards:

  - coding-standards

governance:

  - architecture-review
```

---

# Required Fields

```yaml
name

version

owner

description
```

---

# Optional Fields

```yaml
agents

commands

workflows

templates

governance
```

---

# Dependency Rules

Capabilities may depend on:

```text
Architecture

Governance

Technology

Domain
```

Example:

```yaml
insurance-pack:

  requires:

    - governance-pack

    - architecture-pack
```

---

# Validation Rules

- Unique Name
- Valid Version
- No Circular Dependencies
- Valid References

---

# Capability Categories

```text
Architecture

Technology

Cloud

AI

Governance

Modernization

Delivery

Domain
```

---

# Lifecycle

```text
Draft

Approved

Published

Deprecated

Retired
```
