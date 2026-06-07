# System Overview

## High-Level Architecture

EEIK is a manifest-driven engineering platform.

---

# Architecture Diagram

```text
User
  ↓
Bootstrap Engine
  ↓
Manifest
  ↓
Capability Resolver
  ↓
Capability Packs
  ↓
Repository Generator
  ↓
Generated Workspace
```

---

# Component Overview

## Bootstrap Engine

Purpose:

Discover project requirements.

Output:

```yaml
project-manifest.yaml
```

---

## Manifest

Purpose:

Project source of truth.

Contains:

- Technology Stack
- Governance Profile
- AI Requirements
- Architecture Style

---

## Capability Resolver

Purpose:

Determine required capability packs.

Example:

```yaml
backend:
  java21

cloud:
  aws
```

Resolves:

```yaml
java-pack

aws-pack
```

---

## Capability Packs

Purpose:

Provide reusable intelligence.

Contain:

- Agents
- Standards
- Templates
- Commands
- Workflows

---

## Repository Generator

Purpose:

Create project workspaces.

Outputs:

```text
.github/
.claude/
src/
docs/
```

---

## Agent Factory

Purpose:

Generate project-specific agents.

Input:

```yaml
manifest
```

Output:

```yaml
generated-agents
```

---

## Governance Engine

Purpose:

Apply enterprise controls.

Examples:

- Architecture Reviews
- Security Reviews
- AI Reviews

---

## Knowledge Platform

Purpose:

Capture reusable knowledge.

Sources:

- ADRs
- Incidents
- Reviews
- Postmortems

---

# Repository Lifecycle

```text
Bootstrap
 ↓
Manifest
 ↓
Capabilities
 ↓
Repository Generation
 ↓
Implementation
 ↓
Governance
 ↓
Knowledge Capture
```

---

# Design Principles

1. Manifest First

2. Capability Driven

3. Governance By Default

4. AI Native

5. Knowledge Retaining

6. Vendor Agnostic

7. Reuse Before Creation
