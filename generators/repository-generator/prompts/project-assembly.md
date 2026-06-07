# Project Assembly Prompt

## Purpose

Coordinate multi-pack assembly when a project requires overlapping capability packs.

This prompt handles conflict resolution and ordering when multiple packs contribute to the same output artifact.

---

## Assembly Order

Capability packs are applied in this strict order to avoid conflicts:

```
1. architecture-pack     ← Sets baseline structure and principles
2. java-pack             ← Applies Java-specific layout and standards
3. python-pack           ← Applies Python-specific layout (if applicable)
4. aws-pack              ← Applies CDK stacks and AWS resource naming
5. ai-engineering-pack   ← Applies agentic overlays on top of base structure
6. governance-pack       ← Applies review gates and compliance artifacts
7. domain-pack           ← Domain-specific overlays (insurance/banking/etc)
8. modernization-pack    ← Legacy coexistence adapters (if modernization enabled)
```

---

## Conflict Resolution Rules

When two packs attempt to generate the same artifact:

| Conflict Type              | Resolution                                              |
|----------------------------|---------------------------------------------------------|
| Same file, different content | Later pack in order wins; note conflict in report      |
| Same directory, different structure | Merge — union of both structures               |
| Incompatible standards     | HALT — surface to architect for decision                |
| Duplicate agent definitions | Earlier pack's agent wins; later pack skips            |

---

## Shared Asset Handling

Some assets are consumed by multiple packs:

- `project-manifest.yaml` — read-only by all packs
- `.claude/memory/project-context.md` — written once by assembly, read by all
- `CLAUDE.md` — written once; packs contribute sections via template merging
- `pom.xml` / `pyproject.toml` — managed by tech pack; other packs add dependencies

---

## Template Merging Strategy

For `CLAUDE.md` (multi-pack contribution):

```
Section 1: Project Identity             ← Core (from manifest)
Section 2: Technology Stack             ← Tech pack (java/python)
Section 3: Golden Rules                 ← Tech pack + governance pack
Section 4: Available Agents             ← All packs (merged list)
Section 5: Slash Commands               ← All packs (merged list)
Section 6: Memory Files                 ← Core
Section 7: Domain Context              ← Domain pack (if applicable)
Section 8: Governance Obligations      ← Governance pack
```

---

## Dependency Chain Validation

Before assembly, validate the dependency chain:

```
Check: each pack's dependencies.yaml is satisfied
If dependency is a planned pack (📋): WARN and skip
If dependency is a required pack (✅): proceed
```

Example dependency validation output:

```
Dependency Check:
  architecture-pack   ✅ satisfied (foundational)
  java-pack           ✅ satisfied (depends on architecture-pack ✅)
  aws-pack            ✅ satisfied (depends on architecture-pack ✅)
  ai-engineering-pack ✅ satisfied (depends on architecture-pack ✅)
  governance-pack     ✅ satisfied (depends on architecture-pack ✅)
  banking-pack        ⚠️ planned — domain overlays skipped
```
