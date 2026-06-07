# Generators

This is the actual execution engine of EEIK.

Everything before this point defines:

What should be generated

The generators define:

How it gets generated

The Repository Generator, Agent Factory, Governance Engine, Knowledge Engine, and Capability Resolver all live here.

## Structure

```
generators/
│
├── repository-generator/
├── agent-generator/
├── workflow-generator/
├── command-generator/
├── governance-generator/
├── knowledge-generator/
├── documentation-generator/
├── modernization-generator/
├── capability-selector/
├── model-router/
├── validators/
├── project-analyzer/
└── shared/
```

## generators/repository-generator

### Purpose:
Build entire repositories from manifests.

### Structure

```
repository-generator/
│
├── README.md
│
├── prompts/
│   ├── repository-generation.md
│   ├── project-assembly.md
│   └── dependency-resolution.md
│
├── templates/
│   └── repository-layout.md
│
├── workflows/
│   ├── repository-generation.yaml
│   └── repository-upgrade.yaml
│
└── examples/
    └── insurance-modernization.md
```


## generators/agent-generator

This is arguably EEIK's most important generator.
Instead of storing 300 agents:
Store agent blueprints.

Generate project-specific agents.

### Structure

```
agent-generator/
│
├── README.md
│
├── blueprints/
│   ├── architect.yaml
│   ├── engineer.yaml
│   ├── reviewer.yaml
│   ├── auditor.yaml
│   ├── planner.yaml
│   └── specialist.yaml
│
├── prompts/
│   ├── generate-agent.md
│   ├── generate-memory.md
│   └── generate-evaluation.md
│
├── workflows/
│   └── agent-generation.yaml
│
└── templates/
    ├── agent-template.md
    ├── memory-template.md
    └── evaluation-template.md
```


## generators/workflow-generator

### Purpose:
Generate workflows dynamically.

### Structure

```
workflow-generator/
│
├── templates/
│   ├── design-workflow.yaml
│   ├── review-workflow.yaml
│   └── governance-workflow.yaml
│
├── prompts/
│   └── workflow-generation.md
│
└── examples/
```

### Example Output

```text
workflow:

  architecture-design

steps:

  requirements

  architecture

  review

  approval

  publish
```

## generators/command-generator

### Purpose:
Generate commands from workflows.

### Structure

```
command-generator/
│
├── prompts/
├── templates/
└── examples/
```

### Example

Workflow:

```
Architecture Review
```

Generated Command:

```
/review-architecture
```

## generators/governance-generator

### Purpose:
Create governance assets.

### Structure

```
governance-generator/
│
├── prompts/
├── workflows/
├── templates/
│
└── governance-profiles/
    ├── basic.yaml
    ├── standard.yaml
    ├── regulated.yaml
    └── enterprise.yaml
```

## generators/knowledge-generator

### Purpose:
Create reusable organizational intelligence.

### Structure

```
knowledge-generator/
│
├── prompts/
├── workflows/
├── templates/
│
└── extractors/
    ├── adr-extractor.md
    ├── lesson-extractor.md
    ├── incident-extractor.md
    └── pattern-extractor.md
```

### Example

Input:

```
Production Incident
```

Output:

```
Incident Summary

Lessons Learned

Pattern Candidate

Knowledge Entry
```

## generators/documentation-generator

### Purpose:

Generate documentation.

### Structure
```
documentation-generator/
│
├── prompts/
├── workflows/
└── templates/
```

### Generated Assets

```
README

Architecture

Runbooks

ADRs

RFCs

Wiki Pages
```

## generators/modernization-generator

### Purpose:
Generate modernization artifacts.

### Structure:
```
modernization-generator/
│
├── prompts/
│   ├── analyze-rpg.md
│   ├── analyze-cobol.md
│   ├── analyze-mainframe.md
│   └── create-modernization-roadmap.md
│
├── templates/
│   ├── modernization-roadmap.md
│   ├── strangler-plan.md
│   └── target-state.md
│
└── workflows/
    ├── modernization-analysis.yaml
    └── migration-planning.yaml
```

## generators/capability-selector

### Purpose:
Map manifest → capability packs

### Structure:

```
capability-selector/
│
├── capability-matrix.yaml
├── dependency-matrix.yaml
├── governance-matrix.yaml
└── routing-matrix.yaml
```

## generators/model-router

### Purpose:

Determine model selection.

### Structure

```
model-router/
│
├── routing-policies/
│   ├── architecture.yaml
│   ├── engineering.yaml
│   ├── governance.yaml
│   └── documentation.yaml
│
├── prompts/
└── examples/
```

## generators/validators

### Purpose:
Validate generated outputs.

### Structure

```
validators/
│
├── manifest-validator.md
├── repository-validator.md
├── governance-validator.md
├── agent-validator.md
├── workflow-validator.md
└── documentation-validator.md
```

## generators/shared

### Purpose:
Shared assets used by every generator.

### Structure
```
shared/
│
├── schemas/
│
├── standards/
│
├── prompts/
│
├── examples/
│
└── metadata/
```

##. generators/project-analyzer/

### Structure:

```
project-analyzer/
│
├── prompts/
├── workflows/
├── extractors/
└── reports/
```

## Purpose:
Analyze an existing repository.

Infer:
- Architecture
- Languages
- Frameworks
- CI/CD
- Cloud
- Risks
- Missing Standards

Generate:
project-manifest.yaml

This enables:

```
Existing Legacy Repo
        ↓
/analyze-project
        ↓
Generated Manifest
        ↓
Capability Selection
        ↓
EEIK Adoption
```

That capability dramatically lowers adoption friction because teams rarely start with greenfield projects.
