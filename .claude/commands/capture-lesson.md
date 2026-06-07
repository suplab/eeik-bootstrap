# /capture-lesson — Lessons Learned

Capture a project learning into the knowledge repository.

---

## Usage

```
/capture-lesson "brief description of what was learned"
```

Examples:
- "Flyway fails silently on existing databases without baseline"
- "LangGraph needs recursion_limit or agent runs indefinitely"
- "Testcontainers startup adds 45s to CI — we optimised with shared containers"

---

## Execution

Follow the lesson extraction process from:

```
generators/knowledge-generator/extractors/lesson-extractor.md
```

Steps:

1. Classify: category + severity
2. Ask the 5 context questions (what happened, root cause, fix, prevention, time lost)
3. Determine the next LL-NNN number from `knowledge/lessons-learned/README.md`
4. Write to `knowledge/lessons-learned/LL-{NNN}-{kebab-case-title}.md`
5. Update the index in `knowledge/lessons-learned/README.md`
6. Check if the fix is a promotable pattern

---

## Output

```
✅ Created: knowledge/lessons-learned/LL-004-{title}.md
✅ Updated: knowledge/lessons-learned/README.md

Summary:
  LL-004: {title}
  Category: {category}
  Severity: {severity}
  Time lost: {estimate}

💡 Pattern candidate detected — run /capture-lesson again with the fix approach
   to promote it to knowledge/patterns/
```
