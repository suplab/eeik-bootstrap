# /generate-agent — Agent Factory

Generate a project-specific Claude Code agent from a reusable blueprint.

---

## Usage

```
/generate-agent --blueprint <blueprint> --name <agent-name> [--specialisation <value>]
```

**Parameters:**

| Parameter       | Required | Values                                                                          |
|-----------------|----------|---------------------------------------------------------------------------------|
| `--blueprint`   | Yes      | `architect`, `engineer`, `reviewer`, `auditor`, `specialist`, `investigator`, `coordinator`, `planner` |
| `--name`        | Yes      | Desired agent name in kebab-case (e.g. `claims-triage-specialist`)             |
| `--specialisation` | No    | Narrows the blueprint (e.g. `langgraph` for specialist, `security` for auditor)|

---

## Examples

```bash
# Generate a Java developer agent for the current project
/generate-agent --blueprint engineer --name java-developer

# Generate a LangGraph specialist
/generate-agent --blueprint specialist --name langgraph-engineer --specialisation langgraph

# Generate a GDPR compliance auditor
/generate-agent --blueprint auditor --name gdpr-compliance-reviewer --specialisation compliance

# Generate a multi-agent supervisor/router
/generate-agent --blueprint coordinator --name supervisor-agent --specialisation supervisor
```

---

## Execution

Follow the full generation process from:

```
generators/agent-generator/prompts/generate-agent.md
```

Steps:

1. Load blueprint: `generators/agent-generator/blueprints/{blueprint}.yaml`
2. Read `project-manifest.yaml` to resolve parameters
3. Render all templates with resolved parameter values
4. Apply conditional blocks based on manifest context
5. Write agent file to `.claude/agents/{name}.md`
6. Confirm output with summary

---

## Output Location

```
.claude/agents/{name}.md
```

---

## Available Blueprints

| Blueprint      | Generates Agents Like                                                     |
|----------------|---------------------------------------------------------------------------|
| `architect`    | solution-architect, enterprise-architect, domain-architect, ai-architect  |
| `engineer`     | java-developer, python-engineer, cdk-engineer, frontend-developer         |
| `reviewer`     | code-reviewer, java-reviewer, api-reviewer                                |
| `auditor`      | security-auditor, compliance-reviewer, ai-governance-officer              |
| `specialist`   | langgraph-engineer, rag-specialist, ibmi-expert, kafka-engineer           |
| `investigator` | rca-agent, incident-handler, performance-investigator                     |
| `coordinator`  | supervisor-agent, router-agent, orchestrator-agent                        |
| `planner`      | estimator, project-tracker, sprint-planner                                |

---

## Post-Generation

After generating an agent:

1. Review the generated `.claude/agents/{name}.md`
2. Customise domain-specific knowledge in the constraints section
3. Run a test prompt to verify the agent activates correctly
4. Commit to source control with message: `feat(agents): add {name} agent`
