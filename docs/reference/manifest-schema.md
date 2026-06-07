# Manifest Schema

## Purpose

The Manifest is the canonical source of truth for every EEIK project.

All generators, capability resolvers, governance engines, workflows, and agent factories consume the manifest.

---

# Design Principles

1. Single Source Of Truth
2. Human Readable
3. Machine Validatable
4. Versioned
5. Extensible

---

# Schema Overview

```yaml
schema_version:

project:

technology:

architecture:

cloud:

ai:

governance:

delivery:

modernization:

integrations:

observability:
```

---

# Example Manifest

```yaml
schema_version: 1.0

project:

  name: claims-modernization

  description: Insurance claims modernization

  owner: claims-platform-team

  domain: insurance

technology:

  backend:

    language: java

    version: 21

    framework: spring-boot

  frontend:

    framework: react

  database:

    type: postgresql

architecture:

  style: microservices

cloud:

  provider: aws

ai:

  enabled: true

  pattern: multi-agent

governance:

  profile: enterprise

delivery:

  methodology: agile

modernization:

  enabled: true

  source_platform: ibmi

integrations:

  pattern: event-driven

observability:

  enabled: true
```

---

# Required Fields

```yaml
project.name

project.domain

technology.backend

architecture.style

governance.profile
```

---

# Optional Fields

```yaml
ai

modernization

observability

integrations
```

---

# Consumers

```text
Bootstrap Engine

Capability Resolver

Repository Generator

Agent Factory

Governance Engine
```

---

# Validation Rules

- Project Name Required
- Domain Required
- Governance Profile Required
- Valid Schema Version Required
- Valid Capability Mapping Required

---

# Versioning

```text
Major.Minor
```

Example:

```text
1.0
1.1
2.0
```
