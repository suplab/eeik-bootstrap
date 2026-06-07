# /generate-repo — Repository Generator

Generate a complete project repository scaffold from a validated `project-manifest.yaml`.

---

## Pre-Conditions

Before running this command:

1. `project-manifest.yaml` must exist in the project root
2. `/validate-manifest` must have been run and returned PASS

If either condition is not met, halt and instruct the user to resolve it first.

---

## Execution

Follow the full generation process defined in:

```
generators/repository-generator/prompts/repository-generation.md
```

Execute all 9 steps in sequence:

1. Read + validate the manifest
2. Resolve capability pack dependencies (`generators/repository-generator/prompts/dependency-resolution.md`)
3. Generate directory structure (`generators/repository-generator/templates/repository-layout.md`)
4. Apply technology-specific overlays
5. Apply architecture pattern overlays
6. Apply AI/agentic overlays (if `ai.enabled = true`)
7. Generate infrastructure stubs (CDK stacks, CI/CD workflows)
8. Generate `.claude/` configuration (CLAUDE.md, agents, memory)
9. Output generation report

---

## Capability Pack Assembly

Use the assembly order from:

```
generators/repository-generator/prompts/project-assembly.md
```

Check each required pack against `capability-packs/{pack}/metadata.yaml`.

For any pack marked 📋 (planned): include a WARN in the report but continue.

---

## Agent Auto-Generation

For each agent required by the manifest context, call:

```
generators/agent-generator/prompts/generate-agent.md
```

Standard agent set:
- Always: `architect`, `code-reviewer`
- Java: `java-developer`, `java-tech-lead`, `java-tester`
- AWS: `aws-architect`, `cdk-engineer`
- AI: `ai-architect`, `{framework}-engineer`
- Regulated/Enterprise: `security-auditor`, `compliance-reviewer`
- Enterprise: `arb-reviewer`, `production-readiness-reviewer`

---

## Governance Assets

Select governance profile from:

```
generators/governance-generator/governance-profiles/{profile}.yaml
```

Where `profile` = `manifest.governance.profile`.

Generate all artifacts listed in `generated_artifacts`.

---

## Output

Print the generation report:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  EEIK Repository Generator — Complete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Project:    {name}
Type:       {project_type}
Domain:     {domain}

Generated:
  ✅ / ⚠️ each category with file counts

Packs Applied:
  ✅ / ⚠️ / ❌ each pack

Next Steps:
  1. Review CLAUDE.md and customise
  2. Replace TODO placeholders
  3. Set real values in cdk.context.json
  4. Run /validate-manifest
  5. Push to feature branch
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Error Cases

| Condition                        | Action                                           |
|----------------------------------|--------------------------------------------------|
| No manifest found                | HALT — "Run /bootstrap first to generate a manifest" |
| Manifest fails validation        | HALT — "Run /validate-manifest and fix all errors" |
| Pack marked 📋 (planned)         | WARN — continue without that pack's overlays     |
| AI overlay missing framework     | WARN — skip AI scaffold, flag in report          |
| Regulated domain + basic profile | ERROR — blocked by validate-manifest; won't reach here |
