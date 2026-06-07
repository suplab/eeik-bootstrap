# Generate Agent Prompt

## Purpose

Generate a project-specific Claude Code agent from a blueprint, parameterised by the project manifest.

Activated by `/generate-agent` or called internally by `/generate-repo`.

---

## Inputs

```
blueprint:    One of architect | engineer | reviewer | auditor | specialist | investigator | coordinator | planner
agent_name:   The desired agent name (kebab-case)
manifest:     project-manifest.yaml (for context parameterisation)
```

---

## Generation Process

### Step 1 — Load Blueprint

Read `generators/agent-generator/blueprints/{blueprint}.yaml`.

Extract:
- `fixed` fields (applied unchanged)
- `parameters` (to be resolved from manifest)
- `generation_rules` (templates to render)

### Step 2 — Resolve Parameters

For each parameter in `parameters`:
- If `source` is set: read value from manifest at the specified path
- If `values` is set: use the provided value (from command input)
- If neither: prompt the user

### Step 3 — Render Templates

Substitute resolved parameter values into all `*_template` fields in `generation_rules`.

Apply conditional blocks:
- `if_language_java` → include only if `manifest.technology.backend.language = java`
- `if_governance_regulated` → include only if `manifest.governance.profile in [regulated, enterprise]`
- `if_domain_banking` → include only if `manifest.project.domain = banking`
- etc.

### Step 4 — Generate Agent File

Output a valid Claude Code agent file at `.claude/agents/{agent-name}.md`.

Use the template from `generators/agent-generator/templates/agent-template.md`.

---

## Output Format

```markdown
---
name: {agent_name}
description: {rendered description_template}
---

# {Agent Display Name}

## Role

{role description — derived from blueprint category and specialisation}

## Responsibilities

{rendered responsibilities_template as bullet list}

## Tools

{fixed.tools_allowed as comma-separated list}

## Constraints

{rendered constraints_template as bullet list}

## Output Format

{rendered output_format_template}

## Escalation

{fixed.escalation_strategy}

## Failure Handling

{fixed.failure_handling}
```

---

## Example: Generate java-developer for payment-gateway

Manifest snippet:
```yaml
project.name: payment-gateway
technology.backend.language: java
technology.backend.framework: spring-boot
technology.backend.version: 21
architecture.style: microservices
architecture.patterns: [ddd, outbox]
governance.profile: standard
```

Blueprint: `engineer`
Parameters resolved:
- language = java
- framework = spring-boot
- version = 21
- architecture_style = microservices
- patterns = [ddd, outbox]
- test_tools = "JUnit 5, AssertJ, Mockito 5, Testcontainers"

Generated agent name: `java-developer`

Generated constraints include:
- ✅ `jakarta.* — never javax.* in Spring Boot 3.x` (if_language_java = true)
- ✅ `java.time — never Date or Calendar` (if_language_java = true)
- ❌ `No PII logged in plaintext` (if_governance_regulated = false, skipped)
