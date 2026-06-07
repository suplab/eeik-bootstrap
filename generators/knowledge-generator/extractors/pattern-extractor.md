# Pattern Extractor

## Purpose

Promote a working, approved implementation approach to a reusable knowledge pattern.

Called when a team has solved a problem in a way that should be shared across projects.

---

## Trigger Conditions

A pattern candidate should be extracted when:

- The same solution works in multiple projects
- An agent recommendation is validated by the team
- A novel approach is explicitly approved in architecture review
- A lessons-learned fix reveals a generalizable solution

---

## Extraction Process

### Step 1 — Classify the Pattern

Determine category:
- `Java` — Spring Boot / JPA / testing patterns
- `AWS` — CDK / serverless / networking patterns
- `Agentic AI` — LangGraph / RAG / agent coordination patterns
- `Architecture` — Cross-cutting design patterns
- `Modernization` — Legacy migration patterns

### Step 2 — Gather Pattern Details

- What context does this pattern apply to?
- What problem does it solve?
- What is the exact solution (with code example)?
- What are the trade-offs (pros and cons)?
- When should you NOT use this pattern?
- What patterns does it relate to?

### Step 3 — Write the File

Write to `knowledge/patterns/{category}-{pattern-name}.md`.

### Step 4 — Update Index

Add to `knowledge/patterns/README.md` index table.

---

## Output Format

Follows the structure of existing patterns in `knowledge/patterns/`:

```markdown
# Pattern: {Name}

**Status:** Proposed | Approved
**Domain:**
**Technology:**
**Validated:**

## Context
## Solution
## Example Implementation
## Trade-offs
## When NOT to Use
## Related Patterns
```
