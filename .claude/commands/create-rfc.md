# /create-rfc — Request for Comments

Scaffold a Request for Comments document for proposals that need wider input before a decision is made.

---

## Usage

```
/create-rfc "proposal title"
```

Use an RFC when:
- A decision affects multiple teams and needs broader consensus
- The solution space is still open and you want structured feedback
- The proposal involves significant architectural change or risk

Use `/create-adr` instead when the decision is already made and needs recording.

---

## Execution

1. Determine the next RFC number from `docs/rfcs/` (create if it doesn't exist)
2. Ask the user: "What problem does this RFC solve?"
3. Ask: "What is your proposed solution?"
4. Ask: "What alternatives did you consider?"
5. Ask: "What feedback are you specifically seeking?"
6. Write to `docs/rfcs/RFC-{NNN}-{kebab-case-title}.md`

---

## Output Format

```markdown
# RFC-NNN: {Title}

**Status:** Draft | In Review | Accepted | Rejected | Superseded
**Author:** {name / team}
**Date:** YYYY-MM-DD
**Review Deadline:** YYYY-MM-DD
**Discussion:** {link to PR or discussion thread}

## Summary

{One paragraph describing the proposal}

## Motivation

{What problem does this solve? Why now?}

## Proposed Solution

{The specific change or approach proposed}

## Alternatives Considered

| Alternative | Trade-offs | Reason Not Preferred |
|-------------|------------|---------------------|

## Impact

{Who / what is affected by this change?}

## Open Questions

{Specific questions the author wants reviewers to address}

## Feedback Requested By

{Date — typically 1–2 weeks from creation}
```

---

## Output

```
✅ Created: docs/rfcs/RFC-001-{slug}.md

Next Steps:
  1. Share RFC with affected teams for review
  2. Set a feedback deadline (recommended: 2 weeks)
  3. After decision: convert to /create-adr and link back to this RFC
```
