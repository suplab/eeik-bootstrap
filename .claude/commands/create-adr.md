# /create-adr — Architecture Decision Record

Scaffold a new Architecture Decision Record and write it to `docs/decisions/`.

---

## Usage

```
/create-adr "decision title"
```

The title should be a short, imperative phrase:
- "Use PostgreSQL over DynamoDB for order data"
- "Adopt hexagonal architecture for all Java services"
- "Use LangGraph for multi-agent coordination"

---

## Execution

Follow the ADR extraction process from:

```
generators/knowledge-generator/extractors/adr-extractor.md
```

Steps:

1. Determine the next ADR number from `docs/decisions/`
2. If the user only provided a title, ask the 5 context questions
3. Populate all sections of the ADR template
4. Write to `docs/decisions/ADR-{NNNN}-{kebab-case-title}.md`
5. Add summary to `.claude/memory/decisions.md`
6. Check if this is a cross-cutting decision worth contributing to `knowledge/adr-repository/`

---

## Required Sections

Every ADR must have:
- **Status** — Proposed, Accepted, Deprecated, or Superseded
- **Context** — Why this decision was needed
- **Decision** — What was decided (clear and unambiguous)
- **Rationale** — Why this was chosen
- **Consequences** — Positive, negative, and neutral
- **Alternatives Considered** — What else was evaluated

No placeholder text — all sections must be filled.

---

## Output

```
✅ Created: docs/decisions/ADR-0003-use-postgresql.md
✅ Updated: .claude/memory/decisions.md

Summary:
  ADR-0003: Use PostgreSQL over DynamoDB for order data
  Status: Accepted
  Key decision: Aurora PostgreSQL chosen for relational query requirements
  Review trigger: If order query complexity decreases significantly
```
