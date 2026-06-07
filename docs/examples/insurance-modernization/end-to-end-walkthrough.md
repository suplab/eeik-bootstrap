# Insurance Claims Modernization
## End-to-End EEIK Walkthrough

Version: 1.0

---

# Objective

Demonstrate how EEIK bootstraps and governs a real-world enterprise modernization initiative.

Scenario:

A large insurance company operates a claims platform on IBM i.

Current challenges:

- RPG-based applications
- Tight coupling
- Batch-heavy processing
- Manual document validation
- Limited API capabilities
- Slow release cycles

Target:

Modernize into a cloud-native platform on AWS using Java, React, event-driven architecture, and Agentic AI capabilities.

---

# Current State

## Business Domain

Claims Management

---

## Existing Technology

### Backend

```text
IBM i
RPG
CL Programs
DB2
```

### Integrations

```text
SOAP
File Transfers
Batch Jobs
```

### User Interface

```text
Green Screen
Legacy Web Portals
```

---

# Desired State

## Backend

```text
Java 21
Spring Boot
```

## Frontend

```text
React
```

## Cloud

```text
AWS
```

## Integration

```text
REST APIs
EventBridge
SQS
```

## AI

```text
Agentic Claims Assistants
Document Intelligence
Trustworthiness Review
```

---

# Step 1 — Bootstrap

Command:

```text
/bootstrap
```

---

## Discovery Questions

### Project Type

```text
Modernization
```

### Domain

```text
Insurance
```

### Backend

```text
Java 21
```

### Frontend

```text
React
```

### Cloud

```text
AWS
```

### Architecture

```text
Microservices
```

### AI

```text
Multi-Agent
```

### Modernization

```text
IBM i
```

### Governance

```text
Enterprise
```

---

# Step 2 — Manifest Generation

Generated:

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

Manifest becomes the project source of truth.

---

# Step 3 — Capability Resolution

Selected Capability Packs:

```yaml
selected_packs:

  - architecture-pack

  - modernization-pack

  - java-pack

  - react-pack

  - aws-pack

  - ai-engineering-pack

  - governance-pack

  - delivery-pack

  - observability-pack
```

---

# Step 4 — Governance Resolution

Generated Governance Profile:

```yaml
reviews:

  - architecture-review

  - security-review

  - ai-review

  - production-readiness-review

required_artifacts:

  - architecture-document

  - risk-register

  - adr-log

  - deployment-design
```

---

# Step 5 — Agent Resolution

Generated Agent Recommendations:

```yaml
agents:

  - modernization-architect

  - solution-architect

  - java-architect

  - aws-architect

  - event-driven-architect

  - claims-domain-specialist

  - governance-reviewer

  - security-reviewer

  - ai-reviewer
```

---

# Step 6 — Repository Planning

Generated:

```text
claims-modernization/
```

Repository Plan:

```text
.github/
.claude/

docs/
src/

infrastructure/

architecture/

governance/

delivery/

knowledge/
```

---

# Step 7 — Architecture Generation

Command:

```text
/create-architecture
```

Generated Artifacts:

```text
solution-architecture.md

deployment-architecture.md

integration-architecture.md

event-model.md

nfr.md
```

---

# Target Architecture

```text
React Frontend
        │
API Gateway
        │
Claims Services
        │
EventBridge
        │
Document Services

Validation Services

Payment Services

Notification Services
```

---

# Step 8 — Modernization Analysis

Command:

```text
/analyze-modernization
```

Generated:

```text
Current State Assessment

Dependency Map

Migration Roadmap

Risk Register

Target State Design
```

---

# Dependency Discovery

Example:

```text
RPG Program
     │
     ├── DB2 Table A

     ├── DB2 Table B

     ├── Batch Job X

     └── External Interface Y
```

---

# Step 9 — Migration Planning

Generated Migration Waves

---

## Wave 1

Read-only APIs

Examples:

```text
Policy Lookup

Customer Lookup

Claim Status
```

---

## Wave 2

Transactional Services

Examples:

```text
Claim Submission

Document Upload

Claim Validation
```

---

## Wave 3

Core Processing

Examples:

```text
Claim Assessment

Payment Calculation

Settlement Processing
```

---

## Wave 4

Legacy Retirement

Examples:

```text
RPG Decommission

Batch Decommission

Green Screen Retirement
```

---

# Step 10 — AI Engineering

Command:

```text
/design-agent-platform
```

Generated:

```text
claims-agent-team.md
```

---

## Agent Team

### Claim Intake Agent

Responsibilities:

```text
Validate Submission

Request Missing Documents

Create Claim Record
```

---

### Document Intelligence Agent

Responsibilities:

```text
OCR

Classification

Data Extraction
```

---

### Settlement Advisor Agent

Responsibilities:

```text
Explain Payout

Suggest Settlement Scenarios

Provide Rationale
```

---

### Trustworthiness Agent

Responsibilities:

```text
Detect Hallucinations

Validate Evidence

Check Traceability
```

---

# Step 11 — Governance Reviews

Command:

```text
/run-governance-review
```

---

## Architecture Review

Checks:

```text
Scalability

Maintainability

Integration Strategy

Event Design
```

---

## Security Review

Checks:

```text
IAM

Encryption

Secrets

Network Controls
```

---

## AI Review

Checks:

```text
Prompt Quality

Traceability

Explainability

Human Oversight
```

---

## Production Readiness Review

Checks:

```text
Monitoring

Alerting

Runbooks

Recovery Procedures
```

---

# Step 12 — Knowledge Capture

Command:

```text
/capture-knowledge
```

Generated:

```text
ADR Entries

Lessons Learned

Architecture Decisions

Migration Patterns
```

Knowledge is added to the EEIK knowledge system.

---

# Final Outputs

Generated Artifacts:

```text
project-manifest.yaml

selected-capabilities.yaml

recommended-agents.yaml

governance-profile.yaml

repository-plan.md

solution-architecture.md

modernization-roadmap.md

risk-register.md

claims-agent-team.md
```

---

# Benefits Achieved

## Engineering

- Standardized Architecture
- Faster Startup
- Reusable Patterns

---

## Governance

- Consistent Reviews
- Better Auditability
- Reduced Risk

---

## Modernization

- Incremental Delivery
- Lower Migration Risk
- Faster Time To Value

---

## AI

- Governed Agent Adoption
- Explainability
- Trustworthiness Controls

---

# EEIK Lifecycle Summary

```text
Bootstrap
     ↓

Manifest
     ↓

Capability Selection
     ↓

Agent Resolution
     ↓

Repository Planning
     ↓

Architecture Generation
     ↓

Modernization Analysis
     ↓

Governance Reviews
     ↓

Knowledge Capture
     ↓

Continuous Improvement
```

This walkthrough represents the canonical EEIK flow for a large-scale insurance modernization program.
