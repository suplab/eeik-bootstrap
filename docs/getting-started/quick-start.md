# Quick Start

This guide demonstrates the fastest path to adopting EEIK and generating a fully governed engineering workspace.

---

# What You Will Build

By the end of this guide you will generate:

```text
project-manifest.yaml

selected-capabilities.yaml

recommended-agents.yaml

governance-profile.yaml

repository-plan.md
```

---

# Prerequisites

Required:

- Claude Code
- GitHub Repository
- EEIK Repository

Recommended:

- GitHub Copilot
- AWS Account
- Java 21+
- Python 3.12+

---

# Step 1: Clone EEIK

```bash
git clone https://github.com/<org>/enterprise-engineering-intelligence-kit.git

cd enterprise-engineering-intelligence-kit
```

---

# Step 2: Open Claude Code

Navigate to repository root.

Verify:

```text
.claude/
.github/
bootstrap/
capability-packs/
templates/
generators/
docs/
```

exist.

---

# Step 3: Bootstrap A Project

Execute:

```text
/bootstrap
```

EEIK begins discovery.

---

# Step 4: Answer Discovery Questions

Example:

```text
Project Name:
Claims Modernization

Project Type:
Modernization

Domain:
Insurance

Backend:
Java 21

Frontend:
React

Cloud:
AWS

Architecture:
Microservices

AI:
Multi-Agent

Modernization:
IBM i

Governance:
Enterprise
```

---

# Step 5: Manifest Generation

EEIK creates:

```yaml
project:
  name: claims-modernization

project_type:
  modernization

domain:
  insurance

backend:
  java21

frontend:
  react

cloud:
  aws

architecture:
  microservices

ai:
  multi-agent

modernization:
  ibmi

governance:
  enterprise
```

---

# Step 6: Capability Resolution

EEIK automatically selects:

```yaml
selected-packs:

  - architecture-pack
  - java-pack
  - aws-pack
  - modernization-pack
  - ai-engineering-pack
  - governance-pack
```

---

# Step 7: Agent Recommendation

EEIK recommends:

```yaml
agents:

  - solution-architect
  - modernization-architect
  - java-architect
  - aws-architect
  - governance-reviewer
```

---

# Step 8: Review Generated Plan

Generated:

```text
repository-plan.md
```

Contains:

- Folder Structure
- Capability Packs
- Governance Requirements
- Agent Requirements
- Required Reviews

---

# Step 9: Generate Repository

Execute:

```text
/generate-repository
```

Generated:

```text
.github/
.claude/
src/
docs/
infrastructure/
```

---

# Next Steps

Recommended commands:

```text
/create-architecture

/generate-agents

/run-governance-review

/generate-delivery-assets
```

---

# Typical Adoption Timeline

| Stage | Duration |
|---------|---------|
| Bootstrap | 15 mins |
| Capability Selection | 5 mins |
| Architecture Generation | 1 day |
| Governance Setup | 1 day |
| Repository Generation | 30 mins |

Most teams should become productive within the first week.
