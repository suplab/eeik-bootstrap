---
mode: "agent"
description: "Automated PR review pipeline: correctness → security → performance → test quality → consolidated comment"
---

## Objective

Run a four-stage automated review pipeline on a pull request diff, producing a single consolidated review comment ready to paste into GitHub. Each stage uses a specialist agent. The consolidated output is a complete code review covering correctness, security, performance, and test quality.

---

## How to Use This Workflow

Reference this file in Copilot Chat and provide the PR diff or changed files:

```
#file:.github/prompts/workflows/pr-review-workflow.prompt.md

[Paste the PR diff or list of changed files with their contents]
```

---

## Pipeline Stages

---

### Stage 1 — Code Correctness & Style Review

**Agent:** `#file:.github/prompts/agents/reviewer.prompt.md`

**Input:** The full PR diff or all changed files

**Check for:**
- Correctness bugs: NPEs, broken transactions, incorrect null handling, off-by-one errors
- Architecture violations: domain logic in wrong layer, missing `@Transactional`
- Quality: empty catch blocks, missing logging on exception paths, raw types
- Style: Google Java Style compliance, naming conventions, missing Javadoc
- Test presence: are tests added or updated for the changed logic?

**Output:** Findings with `[BLOCKER]`, `[MAJOR]`, `[MINOR]`, `[NIT]` labels

---

### Stage 2 — Security Scan

**Agent:** `#file:.github/prompts/agents/security-auditor.prompt.md`

**Input:** Same diff/files from Stage 1, plus any `SecurityConfig.java` or `application.yml` changes

**Check for:**
- OWASP Top 10 (2021): injection, broken access control, misconfiguration, vulnerable components
- Hardcoded secrets, credentials in config files
- Missing `@PreAuthorize` on new endpoints
- CORS/CSRF misconfiguration
- Missing `@Valid` on new request body parameters
- Sensitive data in log statements

**Output:** Security findings with Critical / High / Medium / Low severity labels + remediation code

---

### Stage 3 — Performance Review

**Agent:** `#file:.github/prompts/agents/performance-reviewer.prompt.md`

**Input:** Same diff/files

**Check for:**
- N+1 query patterns in new JPA code
- Unbounded queries (no `Pageable`) on new repository methods
- Object allocation in loops
- Missing `try-with-resources` on new closeable resources
- Angular: missing `track` in new `@for` / `*ngFor` directives
- Missing caching on expensive read-only operations added in this PR

**Output:** Performance findings table with impact level + corrected code

---

### Stage 4 — Test Quality Verification

**Agent:** `#file:.github/prompts/agents/test-quality-enforcer.prompt.md`

**Input:** New or changed test files from the diff

**Check for:**
- Empty assertions (`assertTrue(true)`, verify-only tests)
- Tests mocking the class under test
- `Thread.sleep()` usage
- Magic numbers in assertions
- Missing negative/boundary tests for new code
- Test method naming compliance

**Output:** Test quality audit report with findings per test method

---

## Consolidated Review Output

After all four stages, produce the consolidated review in this format:

```markdown
## Pull Request Review

### Overall Verdict
[ ] ✅ Approved — no significant issues
[ ] ⚠️ Approved with comments — address majors before merge
[ ] ❌ Changes requested — blockers must be resolved

---

### Stage 1: Correctness & Style

#### [BLOCKER] ...
#### [MAJOR] ...
#### [MINOR] ...
#### [NIT] ...

---

### Stage 2: Security

#### [CRITICAL] ...
#### [HIGH] ...
#### [MEDIUM] ...

---

### Stage 3: Performance

| Location | Issue | Impact | Fix |
|----------|-------|--------|-----|
| ...      | ...   | ...    | ... |

---

### Stage 4: Test Quality

| Test Method | Anti-Pattern | Fix |
|------------|-------------|-----|
| ...        | ...         | ... |

---

### Summary Checklist

#### Before This PR Can Merge
- [ ] All [BLOCKER] and [CRITICAL] findings resolved
- [ ] All [HIGH] security findings resolved or accepted with documented justification
- [ ] All [MAJOR] correctness findings addressed

#### Recommended (Before or Shortly After Merge)
- [ ] [MEDIUM] performance findings addressed
- [ ] [MAJOR] test quality issues fixed
- [ ] [MINOR] style findings reviewed

#### Well Done ✅
- <1-3 positive notes about what was done well in this PR>
```

---

## Notes on Running the Pipeline

- Run all four stages sequentially — each builds context from the previous
- If Stage 2 finds a Critical security issue, stop and flag it immediately — do not continue the pipeline
- The consolidated output is designed to be pasted directly as a single GitHub PR review comment
- For large PRs (>500 lines changed), focus each stage on the highest-risk files first
