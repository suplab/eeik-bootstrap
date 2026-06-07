# Capability Packs

this is where the actual reusable intelligence lives

A capability pack contains:

- Knowledge
- Rules
- Agents
- Standards

## Structure

```text
capability-packs/
│
├── core/
│   ├── agents/
│   ├── commands/
│   ├── prompts/
│   ├── standards/
│   └── workflows/
├── architecture/
├── java/
├── aws/
├── ai-engineering/
├── governance/
├── react/
├── angular/
├── python/
├── modernization/
├── insurance/
├── delivery/
├── observability/
├── security/
├── data-engineering/
├── machine-learning/
├── devops/
└── enterprise-architecture/

```

## Standard Capability Pack Structure

Every pack follows the same structure.

```text
pack-name/
│
├── README.md
│
├── agents/
├── commands/
├── prompts/
├── standards/
├── templates/
├── workflows/
├── knowledge/
├── examples/
│
├── metadata.yaml
└── dependencies.yaml
```


## capability-packs/architecture

### Purpose
Enterprise architecture guidance.

### Structure

```text
architecture/
│
├── README.md
├── metadata.yaml
├── dependencies.yaml
│
├── agents/
│   ├── solution-architect.md
│   ├── enterprise-architect.md
│   ├── architecture-reviewer.md
│   └── modernization-architect.md
│
├── commands/
│   ├── create-architecture.md
│   ├── review-architecture.md
│   ├── create-adr.md
│   └── create-rfc.md
│
├── standards/
│   ├── architecture-principles.md
│   ├── nfr-standard.md
│   ├── integration-standard.md
│   └── event-driven-standard.md
│
├── templates/
│   ├── architecture-template.md
│   ├── adr-template.md
│   └── rfc-template.md
│
├── workflows/
│   ├── architecture-design.yaml
│   └── architecture-review.yaml
│
└── knowledge/
    ├── reference-architectures.md
    └── architecture-patterns.md
```

## capability-packs/java

### Purpose

Enterprise Java Engineering

### Structure

```text
java/
│
├── agents/
│   ├── java-architect.md
│   ├── spring-boot-engineer.md
│   ├── code-reviewer.md
│   ├── performance-engineer.md
│   └── test-engineer.md
│
├── commands/
│   ├── review-java.md
│   ├── improve-coverage.md
│   └── analyze-performance.md
│
├── standards/
│   ├── java-standard.md
│   ├── spring-standard.md
│   ├── testing-standard.md
│   └── api-standard.md
│
├── templates/
│   ├── service-template.md
│   ├── api-template.md
│   └── event-template.md
│
└── workflows/
    ├── code-review.yaml
    └── service-design.yaml
```

## capability-packs/aws

### Purpose

AWS Cloud Engineering

### Structure

```text
aws/
│
├── agents/
│   ├── aws-architect.md
│   ├── cloud-security-reviewer.md
│   ├── cdk-engineer.md
│   └── terraform-engineer.md
│
├── commands/
│   ├── design-aws.md
│   ├── review-infrastructure.md
│   └── estimate-cloud-cost.md
│
├── standards/
│   ├── aws-standard.md
│   ├── security-standard.md
│   ├── tagging-standard.md
│   └── networking-standard.md
│
├── templates/
│   ├── lambda-template.md
│   ├── ecs-template.md
│   ├── eventbridge-template.md
│   └── api-gateway-template.md
│
└── workflows/
    ├── infrastructure-design.yaml
    └── cloud-review.yaml
```

## capability-packs/ai-engineering

### Purpose

Agentic AI Engineering

### Structure

```text
ai-engineering/
│
├── agents/
│   ├── ai-architect.md
│   ├── langgraph-architect.md
│   ├── rag-specialist.md
│   ├── prompt-engineer.md
│   └── trustworthiness-reviewer.md
│
├── commands/
│   ├── design-agent.md
│   ├── review-agent.md
│   ├── evaluate-agent.md
│   └── design-rag.md
│
├── standards/
│   ├── agent-standard.md
│   ├── prompt-standard.md
│   ├── evaluation-standard.md
│   └── memory-standard.md
│
├── templates/
│   ├── agent-template.md
│   ├── prompt-template.md
│   └── evaluation-template.md
│
└── workflows/
    ├── agent-design.yaml
    └── agent-review.yaml
```

## capability-packs/governance

### Purpose

Enterprise governance and controls.

### Structure

```text
governance/
│
├── agents/
│   ├── architecture-reviewer.md
│   ├── security-reviewer.md
│   ├── ai-reviewer.md
│   ├── compliance-reviewer.md
│   └── production-readiness-reviewer.md
│
├── commands/
│   ├── run-review.md
│   ├── run-prr.md
│   └── run-ai-review.md
│
├── standards/
│   ├── governance-standard.md
│   ├── review-standard.md
│   └── compliance-standard.md
│
├── templates/
│   ├── review-template.md
│   ├── risk-register.md
│   └── decision-log.md
│
└── workflows/
    ├── governance-review.yaml
    └── production-readiness.yaml
```
