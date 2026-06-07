# Knowledge Generator

## Purpose

Extract and capture organizational intelligence from project artifacts.

The Knowledge Generator is the **input mechanism** for the Knowledge Repository. Where the Repository Generator *produces* structure, the Knowledge Generator *captures* learnings back from running projects.

## Design Principle

```
Project produces:
  Incidents, Retrospectives, ADRs, Patterns
        ↓
Knowledge Generator extracts:
  /capture-lesson, /capture-incident, /create-adr, /create-rfc
        ↓
Knowledge Repository enriched:
  knowledge/lessons-learned/
  knowledge/incident-repository/
  knowledge/adr-repository/
  knowledge/patterns/
        ↓
Next project inherits richer intelligence
```

## Extractors

| Extractor | Input | Output |
|-----------|-------|--------|
| [adr-extractor](extractors/adr-extractor.md) | Decision discussion / meeting notes | `docs/decisions/ADR-NNNN.md` |
| [lesson-extractor](extractors/lesson-extractor.md) | Retrospective notes / problem description | `knowledge/lessons-learned/LL-NNN.md` |
| [incident-extractor](extractors/incident-extractor.md) | Incident timeline / symptoms | `knowledge/incident-repository/INC-NNN.md` |
| [pattern-extractor](extractors/pattern-extractor.md) | Working implementation / approved approach | `knowledge/patterns/{pattern}.md` |

## Commands

| Command | Extractor | Output Location |
|---------|-----------|----------------|
| `/create-adr "title"` | adr-extractor | `docs/decisions/` |
| `/create-rfc "title"` | adr-extractor (RFC variant) | `docs/rfcs/` |
| `/capture-lesson "learning"` | lesson-extractor | `knowledge/lessons-learned/` |
| `/capture-incident "summary"` | incident-extractor | `knowledge/incident-repository/` |
| `/memory-update "what changed"` | (direct memory update) | `.claude/memory/*.md` |
