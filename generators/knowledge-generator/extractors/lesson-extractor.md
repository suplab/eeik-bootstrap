# Lesson Extractor

## Purpose

Convert a project learning, retrospective note, or problem encountered into a structured Lessons Learned entry.

Activated by `/capture-lesson "brief description"`.

---

## Input

The user provides:

- A description of what went wrong (or unexpectedly right)
- A retrospective action item
- A problem they spent time diagnosing

---

## Extraction Process

### Step 1 — Classify the Lesson

Determine the category:

`Database` | `Testing` | `AI/Agents` | `AWS` | `Security` | `Architecture` | `CI/CD` | `Governance` | `Delivery` | `Performance`

### Step 2 — Assess Severity

| Severity | Definition |
|----------|-----------|
| CRITICAL | Caused data loss, security breach, or major outage |
| HIGH | Blocked deployment or caused significant rework |
| MEDIUM | Caused notable time loss or technical debt |
| LOW | Minor friction or learning opportunity |

### Step 3 — Gather Details

Ask the user (or infer from the description):

1. "What happened?"
2. "What was the root cause?"
3. "How was it fixed?"
4. "How do we prevent it in the future?"
5. "How much time was lost?"

### Step 4 — Determine Lesson ID

Read `knowledge/lessons-learned/README.md` to find the last LL-NNN number. Increment.

### Step 5 — Write the File

Write to `knowledge/lessons-learned/LL-{NNN}-{kebab-case-title}.md`.

### Step 6 — Update Index

Add to the index table in `knowledge/lessons-learned/README.md`.

### Step 7 — Check for Pattern Promotion

Ask: "Does the fix represent a reusable pattern worth documenting?"

If yes: suggest running `/capture-lesson` output through the pattern-extractor to promote to `knowledge/patterns/`.

---

## Output Format

```markdown
# LL-NNN: {Title}

**Category:** {category}
**Severity:** CRITICAL | HIGH | MEDIUM | LOW
**Captured:** YYYY-MM-DD
**Project Phase:** Foundation | Delivery | Production | Ongoing

---

## What Happened

{Description of the problem}

## Root Cause

{What was the underlying cause}

## Fix

{How it was resolved — include code snippets if relevant}

## Prevention

{How to avoid this in future — should become a standard or checklist item}

## Time Lost

{Estimate of impact}
```
