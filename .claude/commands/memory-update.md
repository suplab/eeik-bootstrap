# /memory-update — Update Project Memory

Update one or more `.claude/memory/` files when significant context changes.

---

## Usage

```
/memory-update "what changed"
```

Examples:
- "We switched from SQS to EventBridge for all async messaging"
- "New constraint: all data must remain in eu-west-1 per legal review"
- "Added payment-service to the service inventory — owner: payments-team"
- "Rejected: approach of using shared schema between order-service and payment-service"

---

## Memory Files

| File | Update When |
|------|------------|
| `project-context.md` | New service added, environment changed, auth pattern changed |
| `domain-glossary.md` | New business term introduced or existing term clarified |
| `decisions.md` | Significant team-level decision made (below ADR threshold) |
| `constraints.md` | New technical, business, or regulatory constraint identified |
| `patterns.md` | New approved pattern or anti-pattern identified |
| `tech-debt.md` | New debt identified, existing debt resolved |
| `rca-tracker.md` | Incident opened, RCA updated, corrective action resolved |
| `rejected-approaches.md` | An approach was considered and explicitly rejected |

---

## Execution

1. Read the user's description of what changed
2. Determine which memory file(s) are affected
3. Read the current content of the relevant file(s)
4. Add or update the relevant section — do not delete existing content unless explicitly told to
5. Confirm which files were updated and what was changed

---

## Rules

- **Never delete** existing memory entries unless explicitly instructed
- **Always append** new entries — memory grows over time
- **Maintain format** — follow the existing structure of each file
- **Date-stamp** all new entries with today's date
- **Cross-reference** — if a decision leads to an ADR, note the ADR link
