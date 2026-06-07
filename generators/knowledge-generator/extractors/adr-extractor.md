# ADR Extractor

## Purpose

Convert a decision discussion into a structured Architecture Decision Record.

Activated by `/create-adr "decision title"`.

---

## Input

The user provides one of:

- A free-form description of the decision being made
- Meeting notes from an architecture discussion
- A Slack thread or email chain summary
- Just the title (extractor will prompt for context)

---

## Extraction Process

### Step 1 — Gather Context

If the user provided only a title, ask:

1. "What problem or question does this decision address?"
2. "What was decided?"
3. "Why was this chosen over alternatives?"
4. "What are the main consequences (positive and negative)?"
5. "What alternatives were considered and rejected?"

### Step 2 — Determine ADR Number

Read `docs/decisions/` to find the highest existing ADR number. Increment by 1.

If `docs/decisions/` doesn't exist, create it and start at ADR-0001.

### Step 3 — Generate ADR

Populate the template from `capability-packs/architecture/templates/adr-template.md` with the extracted information.

Fill every section — do not leave placeholders.

### Step 4 — Check for Cross-Cutting Applicability

Ask: "Does this decision apply to all projects using this technology stack, or only to this project?"

If cross-cutting: suggest contributing to `knowledge/adr-repository/` with prefix `ADR-G`.
If project-specific: write only to `docs/decisions/`.

### Step 5 — Write the File

Write to `docs/decisions/ADR-{NNNN}-{kebab-case-title}.md`.

### Step 6 — Update decisions.md

Add a summary line to `.claude/memory/decisions.md`:

```
### D-{N}: {Title}
**Date:** {today}
**Status:** Accepted
**ADR:** [ADR-{NNNN}](../docs/decisions/ADR-{NNNN}-...)
**Decision:** {one-sentence summary}
```

---

## Output Format

```markdown
# ADR-NNNN: {Title}

**Status:** Proposed | Accepted | Deprecated | Superseded by ADR-XXXX
**Date:** YYYY-MM-DD
**Author:** {name or team}
**Applies To:** {scope — this service, all Java services, all projects, etc.}

## Context

{Why this decision was needed. What problem it solves. What forces are at play.}

## Decision

{What was decided, stated clearly and unambiguously.}

## Rationale

{Why this option was chosen. Key trade-off reasoning.}

## Consequences

**Positive:**
- {benefit}

**Negative:**
- {drawback}

**Neutral:**
- {side effect}

## Alternatives Considered

| Alternative | Reason Rejected |
|-------------|----------------|
| {option} | {reason} |

## Review Trigger

{Under what conditions should this decision be revisited?}
```
