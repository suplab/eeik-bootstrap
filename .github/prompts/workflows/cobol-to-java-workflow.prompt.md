---
mode: "agent"
description: "Mainframe modernization pipeline: extract rules → Java skeleton → architect validation → implement → test → security check"
---

## Objective

Orchestrate the complete modernization of a single COBOL program into a Java/Spring Boot service. This workflow produces a production-quality Java implementation, test suite, and semantic risk documentation. Each step must be completed and reviewed before the next begins — partial migrations are worse than no migration.

---

## How to Use This Workflow

Reference this file in Copilot Chat and provide the COBOL program:

```
#file:.github/prompts/workflows/cobol-to-java-workflow.prompt.md

[Paste the full COBOL program source]
[Paste any copybook definitions]
[Paste any DB2 table schemas]
Context: [Brief description of what this program does in business terms]
```

---

## ⚠️ Pre-Flight Checklist

Before starting this workflow, confirm:

- [ ] You have the complete COBOL source — not a partial extract
- [ ] You have all `COPY` copybook definitions referenced in the program
- [ ] You have the DB2 table schemas for all `EXEC SQL` blocks
- [ ] A Java Spring Boot service exists (or will be created) to host the migrated code
- [ ] A subject-matter expert (SME) is available to validate extracted business rules
- [ ] Test data is available from the COBOL test environment for regression validation

---

## Step 1 — Business Rule Extraction

**Agent:** `#file:.github/prompts/agents/analyst.prompt.md`

**Goal:** Extract and enumerate all business rules embedded in the COBOL program in plain English — before any Java code is written.

**Input:** The full COBOL source

**Produce:**
- [ ] Plain-English summary of what the program does (1 paragraph)
- [ ] Numbered list of all business rules extracted from the procedural logic
- [ ] Data dictionary: all significant `WORKING-STORAGE` fields with business meaning
- [ ] List of I/O operations: which tables are read/written, which files are accessed, which CICS interactions occur
- [ ] Identified ambiguities: logic that is unclear or potentially contains defects in the original

**Gate:** The extracted business rules must be reviewed and confirmed by a business SME or a developer who understands the business domain before proceeding. Proceed only when the rules are agreed.

---

## Step 2 — Java Skeleton with Risk Matrix

**Agent:** `#file:.github/prompts/agents/modernization-expert.prompt.md`

**Goal:** Translate the COBOL structure into a Java service skeleton with full semantic risk documentation.

**Input:** The confirmed business rules from Step 1 + the full COBOL source

**Produce:**
- [ ] COBOL → Java type mapping table (all significant fields)
- [ ] Java service class skeleton — method per COBOL paragraph, `// TODO` for business logic
- [ ] Semantic risk matrix — what changed, what was preserved, what requires human validation
- [ ] All `PERFORM THRU`, `GO TO`, and `ALTER` usages flagged with `// MIGRATION-RISK`
- [ ] All packed-decimal (`COMP-3`) fields mapped to `BigDecimal` — never `double`

**Gate:** The semantic risk matrix must be reviewed. Every `HIGH` risk item requires a documented validation plan before the Java implementation is completed.

---

## Step 3 — Service Boundary Validation

**Agent:** `#file:.github/prompts/agents/architect.prompt.md`

**Goal:** Validate that the migrated Java class fits correctly into the target microservice architecture.

**Input:** The Java skeleton from Step 2 + the target system's architecture documentation (if available)

**Produce:**
- [ ] Confirmation of which Java service (existing or new) owns this migrated logic
- [ ] Assessment of data model: does the COBOL data map to an existing JPA entity or does a new one need to be created?
- [ ] Integration point design: how does the new Java service expose this functionality? REST endpoint? Domain service method? Spring Batch step?
- [ ] ADR if the migration requires a new architectural decision
- [ ] Any cross-cutting concerns to address: transaction boundaries, distributed consistency, idempotency

**Gate:** Architecture review must confirm the service boundary before full implementation begins.

---

## Step 4 — Complete Java Implementation

**Agent:** `#file:.github/prompts/agents/developer.prompt.md`

**Goal:** Complete the Java service skeleton into production-ready, compilable code.

**Input:** The skeleton from Step 2 + the architecture decisions from Step 3 + the business rules from Step 1

**Produce:**
- [ ] Complete Java service class — all `// TODO` stubs replaced with real implementations
- [ ] JPA entity (if new) or integration with existing entity
- [ ] Repository interface (if new data access is needed)
- [ ] DTO records for the service's public API
- [ ] MapStruct mapper (if entity ↔ DTO conversion is needed)
- [ ] SLF4J logging on all significant operations and error paths
- [ ] Javadoc referencing the original COBOL program name in the class-level comment
- [ ] `// MIGRATION-RISK` comments retained from Step 2 — do not remove them

**Gate:** Code must compile. All `// MIGRATION-RISK` comments from Step 2 must remain visible until formally resolved by SME validation.

---

## Step 5 — Test Generation Against Business Rules

**Agent:** `#file:.github/prompts/agents/tester.prompt.md`

**Goal:** Generate tests that validate the Java implementation against the business rules extracted in Step 1.

**Input:** The completed Java implementation from Step 4 + the business rules list from Step 1

**Produce:**
- [ ] Unit test class for the migrated service
- [ ] One test per extracted business rule from Step 1 — each test verifies a specific rule
- [ ] Test data derived from real COBOL test values (if provided)
- [ ] Tests for all `SQLCODE` handling paths mapped in Step 2
- [ ] Tests for all `CICS RESP` handling paths mapped in Step 2
- [ ] `@ParameterizedTest` for any business rules with multiple input variants
- [ ] Assertions use `BigDecimal.compareTo()` for monetary values — not `equals()` (scale-sensitive)

**Note:** These tests are the regression baseline for the migration. They must be preserved and run in CI for the lifetime of the migrated service.

---

## Step 6 — Security Audit of Migrated Logic

**Agent:** `#file:.github/prompts/agents/security-auditor.prompt.md`

**Goal:** Ensure that the migration did not introduce security vulnerabilities — particularly SQL injection risks that may have been hidden in the COBOL's host variable pattern.

**Input:** The completed Java implementation from Step 4

**Check specifically:**
- [ ] All DB2 SQL from `EXEC SQL` blocks is using parameterized queries in Java — no string concatenation
- [ ] Input validation applied to data that was previously constrained by COBOL `PIC` clause lengths
- [ ] No sensitive data (customer PII, account balances) logged
- [ ] Authorization: the migrated service method has appropriate access control
- [ ] Any CICS security context (transaction authorization) has been replicated in Spring Security

**Produce:**
- [ ] Security findings report
- [ ] Confirmation that all SQL is parameterized
- [ ] Remediation code for any findings

**Gate:** All Critical and High findings must be resolved before this workflow is marked complete.

---

## Workflow Completion Checklist

```markdown
## COBOL Migration Completion Checklist — {PROGRAM-ID}

### Documentation
- [ ] Business rules extracted and SME-confirmed (Step 1)
- [ ] Semantic risk matrix produced (Step 2)
- [ ] All HIGH risks have a documented validation plan
- [ ] ADR produced for architectural decisions (Step 3)

### Implementation
- [ ] Java service compiles
- [ ] All // MIGRATION-RISK comments addressed or retained with open ticket reference
- [ ] No BigDecimal scale mismatches identified
- [ ] Javadoc references original COBOL program name

### Testing
- [ ] One test per business rule from Step 1
- [ ] All SQL error paths tested
- [ ] Regression test results match COBOL test results (where available)
- [ ] Tests committed to CI pipeline

### Security
- [ ] All SQL parameterized
- [ ] No sensitive data in logs
- [ ] Authorization applied
- [ ] No Critical or High security findings open

### Sign-off
- [ ] Business SME validated business rule extraction (Step 1)
- [ ] Architect approved service boundary (Step 3)
- [ ] Security audit passed (Step 6)
- [ ] Code review approved (recommend running pr-review-workflow.prompt.md on the final diff)
```
