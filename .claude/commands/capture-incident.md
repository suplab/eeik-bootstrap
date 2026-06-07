# /capture-incident — Post-Mortem Capture

Capture a production incident post-mortem into the knowledge repository.

---

## Usage

```
/capture-incident "brief symptom summary"
```

Examples:
- "Outbox publisher stopped processing — 2h event delay"
- "ECS task OOM killed under load — service unavailable for 45 minutes"
- "CDK deployment failed due to IAM permissions gap"

---

## When to Run

After a P1 or P2 incident is **fully resolved** and the immediate incident response is complete.

For **live incidents**, use `/incident "severity: P1, service: name, symptom: description"` first.

---

## Execution

Follow the incident extraction process from:

```
generators/knowledge-generator/extractors/incident-extractor.md
```

Steps:

1. Classify severity (P1/P2/P3/P4)
2. Build the incident timeline
3. Facilitate 5-Whys root cause analysis
4. Define corrective actions (with owner + due date per action)
5. Identify prevention opportunities
6. Determine next INC-NNN number from `knowledge/incident-repository/README.md`
7. Write to `knowledge/incident-repository/INC-{NNN}-{slug}.md`
8. Update index in `knowledge/incident-repository/README.md`
9. Update `.claude/memory/rca-tracker.md` resolved section

---

## Output

```
✅ Created: knowledge/incident-repository/INC-002-{slug}.md
✅ Updated: knowledge/incident-repository/README.md
✅ Updated: .claude/memory/rca-tracker.md

Summary:
  INC-002: {summary}
  Severity: P{n}
  Duration: {duration}
  Root Cause: {one-sentence summary}
  Corrective Actions: {n} defined, {n} owners assigned

Next Steps:
  - Assign owners for each corrective action
  - Track progress in rca-tracker.md
  - Consider runbook creation for faster MTTR next time
```
