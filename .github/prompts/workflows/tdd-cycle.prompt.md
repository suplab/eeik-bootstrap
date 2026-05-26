---
mode: "agent"
description: "Test-Driven Development cycle: analyst defines tests → tester writes failing tests → developer implements → reviewer refactors → coverage confirms"
---

## Objective

Guide a complete Test-Driven Development (TDD) cycle for a single method, class, or feature. The cycle follows Red → Green → Refactor with specialist agents at each phase.

---

## How to Use This Workflow

Reference this file in Copilot Chat and describe what you want to build:

```
#file:.github/prompts/workflows/tdd-cycle.prompt.md

Build: [Describe the method, class, or feature you want to implement using TDD]
```

---

## TDD Cycle Overview

```
Red Phase:    Analyst → Tester
              (Define expectations → Write failing tests)

Green Phase:  Developer
              (Write minimal implementation to pass the tests)

Refactor:     Reviewer
              (Clean up the implementation without breaking tests)

Confirm:      Coverage Enforcer
              (Verify no branches were missed)
```

---

## Phase 1: RED — Define Test Cases

**Agent:** `#file:.github/prompts/agents/analyst.prompt.md`

**Goal:** Define what the implementation must do before writing a single line of production code.

**Input:** Your description of the feature/method/class to implement

**Produce:**
- [ ] List of test scenarios in `methodName_scenario_expectedResult` format
- [ ] For each scenario: input values, preconditions, expected output or exception
- [ ] Edge cases explicitly identified: null input, empty collections, boundary values, concurrent access
- [ ] Scenarios for every error path: what exceptions should be thrown and when

**Example output:**
```
Scenarios for OrderService.createOrder():
1. createOrder_validRequest_persistsAndReturnsOrderWithPendingStatus
2. createOrder_nullRequest_throwsIllegalArgumentException
3. createOrder_emptyLineItems_throwsIllegalArgumentException
4. createOrder_customerNotFound_throwsCustomerNotFoundException
5. createOrder_exceedsCreditLimit_throwsInsufficientCreditException
6. createOrder_repositorySaveFailure_propagatesException
```

**Gate:** Do not write any test or production code until all scenarios are listed and reviewed.

---

## Phase 2: RED — Write Failing Tests

**Agent:** `#file:.github/prompts/agents/tester.prompt.md`

**Goal:** Write tests that fail because the implementation does not exist yet.

**Input:** The scenario list from Phase 1

**Produce:**
- [ ] Complete JUnit 5 test class with one test method per scenario
- [ ] `@Mock` declarations for all dependencies
- [ ] `@InjectMocks` pointing to the class to be implemented
- [ ] Test data factory methods for all test inputs
- [ ] Each test method annotated with `@Test` and containing arrange/act/assert structure
- [ ] The `@InjectMocks` class does not exist yet — the test class should compile but every test should fail with `NullPointerException` or `UnsupportedOperationException`

**Verification:** Confirm mentally that every test would currently fail. If a test would accidentally pass without an implementation, it is not a valid red-phase test.

---

## Phase 3: GREEN — Minimal Implementation

**Agent:** `#file:.github/prompts/agents/developer.prompt.md`

**Goal:** Write the minimal production code needed to make all the failing tests pass. No more.

**Input:** The test class from Phase 2 + the scenario list from Phase 1

**Rules for this phase:**
- Implement **only** what is needed to make the listed tests pass
- Do not add methods, features, or abstractions not covered by the existing tests
- Do not optimise — correctness first
- Stub or simplify complex parts as `// TODO: implement properly` — tests must pass first
- Add SLF4J logging even in the green phase — it is not gold-plating, it is required

**Produce:**
- [ ] Complete implementation class — compilable and all tests passing
- [ ] Confirmation of which tests each implementation decision targets

**Gate:** Run the tests (mentally or in IDE). All tests must pass. No test must be modified to make it pass.

---

## Phase 4: REFACTOR — Clean Up

**Agent:** `#file:.github/prompts/agents/reviewer.prompt.md`

**Goal:** Improve the implementation without changing its behaviour (all tests must still pass after refactoring).

**Input:** The passing implementation from Phase 3 + the test class

**Apply:**
- Extract long methods (> 20 lines) into named private methods
- Replace magic numbers with named constants
- Apply Java 17/21 idioms where appropriate (records, pattern matching, switch expressions)
- Apply Google Java Style formatting
- Ensure Javadoc is complete on all public methods
- Review for SOLID violations introduced during the green phase

**Gate:** After every refactoring, confirm (mentally) that all tests from Phase 2 still pass. If a refactoring would change the public API, flag it — it requires re-running Phase 2.

---

## Phase 5: CONFIRM — Coverage Verification

**Agent:** `#file:.github/prompts/agents/coverage-enforcer.prompt.md`

**Goal:** Verify that the tests from Phase 2 cover all branches in the refactored implementation.

**Input:** The refactored implementation from Phase 4 + the test class from Phase 2

**Produce:**
- [ ] Coverage gap report — any branches not covered by the Phase 2 tests
- [ ] For each gap: a new test scenario to add (return to Phase 2 if gaps are significant)
- [ ] Confirmation that all `if`/`else`, `catch`, and `Optional.empty()` paths are tested

**Gate:** Business logic coverage must be ≥ 80% line, ≥ 70% branch. If gaps exist, add the missing tests and re-confirm.

---

## Cycle Summary

```
Phase 1 (Analyst)    → Scenario list
Phase 2 (Tester)     → Failing test class
Phase 3 (Developer)  → Passing implementation
Phase 4 (Reviewer)   → Refactored, clean code
Phase 5 (Coverage)   → Confirmed coverage
```

**Done when:** All tests pass, coverage thresholds met, no `[BLOCKER]` or `[MAJOR]` findings from the Reviewer.
