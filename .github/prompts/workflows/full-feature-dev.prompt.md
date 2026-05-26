---
mode: "agent"
description: "End-to-end feature development workflow: analyst → architect → developer → tester → coverage → reviewer"
---

## Objective

Orchestrate the full development lifecycle for a new feature — from requirements to production-ready, reviewed code — using the team's specialist agents in sequence. Each step builds on the output of the previous step.

---

## How to Use This Workflow

Reference this file in Copilot Chat, then provide your feature description as the initial input. Work through each step in sequence, using the output of each step as the input to the next.

```
#file:.github/prompts/workflows/full-feature-dev.prompt.md

Feature: [Describe the feature here]
```

---

## Workflow Steps

---

### Step 1 — Requirements Analysis

**Agent:** `#file:.github/prompts/agents/analyst.prompt.md`

**Input:** Your feature description (user story, business requirement, or stakeholder description)

**Produce:**
- [ ] Gherkin acceptance criteria (Given/When/Then) for all scenarios
- [ ] OpenAPI 3.0 contract for any new endpoints
- [ ] Data model description for any new entities
- [ ] List of ambiguities and assumptions

**Gate:** Do not proceed to Step 2 until all ambiguities are resolved or explicitly accepted.

---

### Step 2 — Architecture Validation

**Agent:** `#file:.github/prompts/agents/architect.prompt.md`

**Input:** The acceptance criteria and API contract from Step 1

**Produce:**
- [ ] Bounded context confirmation — which domain does this feature belong to?
- [ ] Component design — what new classes/services are needed?
- [ ] Dependency assessment — does this feature require new infrastructure?
- [ ] ADR (Architecture Decision Record) if a significant design decision is made
- [ ] Sequence diagram for the main flow (Mermaid)

**Gate:** Architecture must be approved before implementation begins. Any `[COUPLING]` violations flagged by the Architect must be resolved in the design.

---

### Step 3 — Implementation

**Agent:** `#file:.github/prompts/agents/developer.prompt.md`

**Input:** The approved design from Step 2 and the API contract from Step 1

**Produce:**
- [ ] All new Java files: entity, repository, service, controller, DTOs, mapper, exception
- [ ] `application.yml` additions for new config properties
- [ ] Google Java Style applied throughout
- [ ] SLF4J logging on all service methods and exception paths
- [ ] Javadoc on all public methods
- [ ] Any dependency additions flagged with `// REQUIRES:` comments

**Gate:** Code must compile (mentally verified). No `System.out.println`, no field `@Autowired`, no raw types.

---

### Step 4 — Test Generation

**Agent:** `#file:.github/prompts/agents/tester.prompt.md`

**Input:** All implementation files from Step 3

**Produce:**
- [ ] Unit test class for each service class (`*Test.java`)
- [ ] Integration test for each controller (`*IT.java`) using `@WebMvcTest` or `@SpringBootTest`
- [ ] Repository test if custom queries are present (`@DataJpaTest` + Testcontainers)
- [ ] Test data factory class
- [ ] All happy paths, null/not-found paths, invalid input paths, and exception paths covered

**Gate:** Tests must be meaningful — every test method must have at least one assertion on a business outcome.

---

### Step 5 — Coverage Verification

**Agent:** `#file:.github/prompts/agents/coverage-enforcer.prompt.md`

**Input:** The implementation files from Step 3 and the test files from Step 4

**Produce:**
- [ ] Coverage gap report — any uncovered branches identified
- [ ] Additional targeted tests for any gaps found
- [ ] Confirmation that all `if`/`else` branches have at least one true-branch and one false-branch test
- [ ] Confirmation that all `catch` blocks have a triggering test

**Gate:** Business logic classes must have ≥ 80% line coverage and ≥ 70% branch coverage before proceeding.

---

### Step 6 — Final Review

**Agent:** `#file:.github/prompts/agents/reviewer.prompt.md`

**Input:** All implementation and test files from Steps 3–5

**Produce:**
- [ ] Structured code review with `[BLOCKER]`, `[MAJOR]`, `[MINOR]`, `[NIT]` findings
- [ ] Verdict: ready to merge / merge after fixes / rework needed
- [ ] PR description template (ready to paste into GitHub)

**Gate:** All `[BLOCKER]` findings must be resolved. `[MAJOR]` findings must be resolved or have a documented justification. PR can be raised only after this step is complete.

---

## PR Description Template

After completing all steps, use this template for the PR:

```markdown
## Summary
- [What was implemented]
- [Why this feature is needed]
- [Key design decisions made]

## Acceptance Criteria
[Paste from Step 1]

## Testing
- [ ] Unit tests added for all service methods
- [ ] Integration test added for controller endpoints
- [ ] Coverage verified: ≥80% line, ≥70% branch on business logic

## Architecture Notes
[Any ADRs or design decisions from Step 2]

## Reviewer Notes
[Any known limitations or follow-up tickets]
```

---

## Aborting the Workflow

If at any step the output reveals that the requirements are fundamentally ambiguous, the architecture would require a major refactor of existing code, or the implementation would introduce a security vulnerability, **stop the workflow** and return to the appropriate step to resolve the issue before proceeding.
