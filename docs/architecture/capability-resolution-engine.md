# Capability Resolution Engine

## Purpose

The Capability Resolution Engine is responsible for translating project requirements into reusable engineering intelligence.

It acts as the bridge between:

```text
Project Requirements
```

and

```text
Capability Packs
```

---

# Problem Statement

Projects describe:

- Business Goals
- Technology Stack
- Architecture Style
- Governance Needs
- Modernization Needs

However, projects should not need to manually determine:

- Required Agents
- Required Workflows
- Required Templates
- Required Governance Controls

The Capability Resolution Engine performs this mapping automatically.

---

# Architecture Overview

```text
Manifest
    │
    ▼
Capability Resolution Engine
    │
    ▼
Selected Capability Packs
    │
    ▼
Repository Generation
```

---

# Inputs

Primary Input:

```yaml
project-manifest.yaml
```

Example:

```yaml
project:
  name: claims-modernization

backend:
  java21

frontend:
  react

cloud:
  aws

architecture:
  microservices

domain:
  insurance

modernization:
  ibmi
```

---

# Resolution Process

## Step 1

Read Manifest

Extract:

```text
Backend

Frontend

Cloud

Architecture

Domain

Governance

AI

Modernization
```

---

## Step 2

Load Capability Matrix

Example:

```yaml
backend:

  java21:
    - java-pack

cloud:

  aws:
    - aws-pack

frontend:

  react:
    - react-pack
```

---

## Step 3

Resolve Dependencies

Example:

```text
insurance-pack
```

requires:

```text
governance-pack

architecture-pack
```

---

## Step 4

Merge Results

Generate:

```yaml
selected-packs:
  - architecture-pack
  - governance-pack
  - java-pack
  - react-pack
  - aws-pack
  - insurance-pack
```

---

## Step 5

Validate Selection

Checks:

- Missing Dependencies
- Circular Dependencies
- Deprecated Packs
- Version Conflicts

---

# Capability Matrix

Purpose:

Map requirements to capabilities.

Example:

```yaml
domain:

  insurance:
    - insurance-pack

  banking:
    - banking-pack

  healthcare:
    - healthcare-pack
```

---

# Dependency Matrix

Purpose:

Determine transitive dependencies.

Example:

```yaml
insurance-pack:

  requires:

    - governance-pack

    - architecture-pack
```

---

# Governance Resolution

Capability selection may trigger governance requirements.

Example:

```yaml
modernization:
  ibmi
```

Automatically enables:

```text
Architecture Review

Migration Review

Production Readiness Review
```

---

# AI Resolution

Example:

```yaml
ai:
  multi-agent
```

Automatically enables:

```text
agent-platform-pack

agent-governance-pack
```

---

# Outputs

Generated:

```yaml
selected-capabilities.yaml
```

Example:

```yaml
selected-packs:

  - architecture-pack

  - governance-pack

  - java-pack

  - aws-pack

  - modernization-pack
```

---

# Design Principles

1. Deterministic

2. Traceable

3. Extensible

4. Versioned

5. Governed

---

# Success Criteria

- Accurate Selection
- Dependency Integrity
- Governance Coverage
- Explainability

---

# Future Enhancements

- Intelligent Recommendations
- Usage-Based Optimization
- Organizational Learning
- Capability Scoring
