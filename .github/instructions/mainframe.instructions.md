---
applyTo: "**/*.cbl, **/*.cob, **/*.asm, **/*.hlasm, **/*.jcl, **/*.cpy, **/*.pco"
---

## Context

This instruction file applies to mainframe source files: IBM COBOL programs, High Level Assembler (HLASM) source, Job Control Language (JCL), and COBOL copybooks. These programs run on IBM z/OS and interact with CICS (transaction processing), DB2 z/OS (relational database), VSAM (indexed file system), and QSAM (sequential datasets). This context is used both for understanding existing programs and for generating migration artifacts. When explaining COBOL, always note the Java equivalent to assist modernization engineers.

---

## COBOL Coding Standards

- **IBM Enterprise COBOL 6.x** syntax — dialect-specific features are acceptable
- Respect the four division structure: `IDENTIFICATION`, `ENVIRONMENT`, `DATA`, `PROCEDURE`
- Every program must have a `PROGRAM-ID` in `IDENTIFICATION DIVISION`
- `WORKING-STORAGE SECTION` holds persistent data for the life of the program
- `LOCAL-STORAGE SECTION` holds thread-local data (re-initialized on each CICS task)
- `LINKAGE SECTION` defines parameters passed via `COMMAREA` or `CALL` interface
- Column layout (fixed-format): columns 1–6 sequence, column 7 indicator (`*` = comment, `-` = continuation), columns 8–11 Area A, columns 12–72 Area B, columns 73–80 identification
- **Copybooks:** Use `COPY` statements for shared record layouts — never inline what belongs in a copybook. Copybooks live in the `COPYLIB` or equivalent library
- **Host variable naming:** DB2 host variables are prefixed `HV-` (e.g., `HV-CUSTOMER-ID`)
- **Return code:** Return code stored in `RETURN-CODE` special register or passed via `WS-RETURN-CODE`

---

## COBOL Patterns

### Basic Program Structure

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. CUSTINQ.
       AUTHOR.     ENTERPRISE TEAM.
      *----------------------------------------------------------------*
      * DESCRIPTION: Customer inquiry program                          *
      * CALLED BY:   CICS transaction CINQ                             *
      *----------------------------------------------------------------*
       ENVIRONMENT DIVISION.
       CONFIGURATION SECTION.
       SOURCE-COMPUTER. IBM-ZOS.
       OBJECT-COMPUTER. IBM-ZOS.

       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-RETURN-CODE         PIC S9(4)  COMP VALUE ZEROS.
       01 WS-CUSTOMER-RECORD.
          05 WS-CUST-ID          PIC 9(10)        VALUE ZEROS.
          05 WS-CUST-NAME        PIC X(50)        VALUE SPACES.
          05 WS-CUST-BALANCE     PIC S9(13)V99 COMP-3 VALUE ZEROS.

       LINKAGE SECTION.
       01 DFHCOMMAREA.
          05 CA-REQUEST-TYPE     PIC X(1).
          05 CA-CUSTOMER-ID      PIC 9(10).
          05 CA-RESPONSE-CODE    PIC S9(4)  COMP.

       PROCEDURE DIVISION.
           PERFORM 1000-INITIALIZE
           PERFORM 2000-PROCESS
           PERFORM 9000-RETURN
           STOP RUN.

       1000-INITIALIZE.
           MOVE ZEROS TO WS-RETURN-CODE.

       2000-PROCESS.
           EVALUATE CA-REQUEST-TYPE
               WHEN 'I'
                   PERFORM 2100-INQUIRE-CUSTOMER
               WHEN OTHER
                   MOVE 8 TO CA-RESPONSE-CODE
           END-EVALUATE.

       2100-INQUIRE-CUSTOMER.
           MOVE CA-CUSTOMER-ID TO HV-CUST-ID
           EXEC SQL
               SELECT CUST_NAME, CUST_BALANCE
               INTO   :HV-CUST-NAME, :HV-CUST-BALANCE
               FROM   SCHEMA.CUSTOMERS
               WHERE  CUST_ID = :HV-CUST-ID
           END-EXEC
           EVALUATE SQLCODE
               WHEN 0
                   MOVE HV-CUST-NAME    TO WS-CUST-NAME
                   MOVE HV-CUST-BALANCE TO WS-CUST-BALANCE
               WHEN 100
                   MOVE 4 TO CA-RESPONSE-CODE
               WHEN OTHER
                   MOVE 12 TO CA-RESPONSE-CODE
           END-EVALUATE.

       9000-RETURN.
           EXEC CICS RETURN END-EXEC.
```

### DB2 Embedded SQL Host Variables

```cobol
       WORKING-STORAGE SECTION.
      * DB2 SQLCA - always include for SQLCODE checking
           EXEC SQL INCLUDE SQLCA END-EXEC.
      * Host variables - prefix HV-
       01 HV-CUST-ID              PIC 9(10)         VALUE ZEROS.
       01 HV-CUST-NAME            PIC X(50)         VALUE SPACES.
       01 HV-CUST-BALANCE         PIC S9(13)V99 COMP-3 VALUE ZEROS.
       01 HV-NULL-IND             PIC S9(4) COMP     VALUE ZEROS.
```

### CICS Command Patterns

```cobol
      * Read from CICS COMMAREA
           EXEC CICS
               ADDRESS COMMAREA(WS-COMMAREA-PTR)
               LENGTH(WS-COMMAREA-LEN)
           END-EXEC.

      * Read a VSAM file via CICS
           EXEC CICS READ
               FILE('CUSTFILE')
               INTO(WS-CUSTOMER-RECORD)
               RIDFLD(WS-CUST-ID)
               RESP(WS-CICS-RESP)
               RESP2(WS-CICS-RESP2)
           END-EXEC.
           EVALUATE WS-CICS-RESP
               WHEN DFHRESP(NORMAL)   CONTINUE
               WHEN DFHRESP(NOTFND)   MOVE 4 TO WS-RETURN-CODE
               WHEN OTHER             MOVE 12 TO WS-RETURN-CODE
           END-EVALUATE.
```

### COBOL → Java Mapping Reference

| COBOL Construct | Java Equivalent |
|----------------|----------------|
| `PIC 9(10)V99` | `BigDecimal` with scale 2 |
| `PIC X(50)` | `String` (max 50 chars) |
| `PIC S9(4) COMP` | `short` or `int` |
| `PIC S9(9) COMP` | `int` or `long` |
| `COMP-3` (packed decimal) | `BigDecimal` |
| `WORKING-STORAGE` fields | Instance fields or method-local variables |
| `LOCAL-STORAGE` fields | Method-local variables (re-initialized per call) |
| `LINKAGE SECTION` | Method parameters |
| `PERFORM UNTIL` | `while` loop |
| `PERFORM n TIMES` | `for (int i = 0; i < n; i++)` loop |
| `EVALUATE` | `switch` expression |
| `COPY` copybook | Java interface, abstract class, or shared record |
| `EXEC SQL ... END-EXEC` | Spring Data JPA `@Query` or `JdbcTemplate` |
| `EXEC CICS ... END-EXEC` | REST endpoint call or message queue |
| `MOVE SPACES TO field` | `field = "";` or `field = null;` |
| `MOVE ZEROS TO field` | `field = 0;` or `BigDecimal.ZERO` |
| `INSPECT ... TALLYING` | String processing with regex or streams |
| `STRING ... INTO` | `String.format()` or `StringBuilder` |

---

## Anti-Patterns — Flag and Explain

| Pattern | Action |
|---------|--------|
| `ALTER verb` | **Do not generate.** Flag as deprecated and unmaintainable — restructure using `EVALUATE` |
| `GO TO label` | Flag — suggest `PERFORM` with structured paragraphs instead |
| `PERFORM THRU` with fall-through | Flag — risk of unintended paragraph execution; restructure as explicit `PERFORM` calls |
| Dynamic SQL from input fields | Flag as SQL injection risk — explain parameterized host variables |
| `STOP RUN` inside nested `PERFORM` | Flag — causes program termination from a subroutine, not just the paragraph |
| Missing `EVALUATE SQLCODE` after `EXEC SQL` | Flag — all DB2 operations must check `SQLCODE` |
| Missing `RESP` on `EXEC CICS` | Flag — CICS errors must be handled via `RESP`/`RESP2`, not `ABEND` assumption |

---

## JCL Standards

```jcl
//JOBNAME  JOB (ACCT),'DESCRIPTION',CLASS=A,MSGCLASS=X,
//             NOTIFY=&SYSUID,MSGLEVEL=(1,1)
//*--------------------------------------------------------------------*
//* Step 1: Run the customer inquiry program                           *
//*--------------------------------------------------------------------*
//STEP1    EXEC PGM=CUSTINQ,REGION=0M
//STEPLIB  DD   DSN=LOAD.LIBRARY,DISP=SHR
//SYSOUT   DD   SYSOUT=*
//SYSPRINT DD   SYSOUT=*
//INPUT    DD   DSN=INPUT.DATASET,DISP=SHR
//OUTPUT   DD   DSN=OUTPUT.DATASET,
//             DISP=(NEW,CATLG,DELETE),
//             SPACE=(CYL,(10,5),RLSE),
//             DCB=(RECFM=FB,LRECL=80,BLKSIZE=0)
```

---

## Assembler (HLASM) Standards

- Register conventions: R0–R1 = parameters/return, R13 = save area, R14 = return address, R15 = return code / entry point
- Always save registers at entry with `STM R14,R12,12(R13)` and restore with `LM R14,R12,12(R13)`
- Use standard save area linkage: `SAVE (14,12)` / `RETURN (14,12)`
- Macro invocations should be documented with a preceding comment explaining the purpose
- DSECT definitions for mapped storage areas must be named descriptively

---

## Modernization Context

When analyzing COBOL for modernization:

1. **Identify bounded contexts** — map program groups to Java microservice candidates
2. **Extract business rules** — separate computation logic from I/O and infrastructure
3. **Flag semantic risks** — where COBOL numeric precision (`COMP-3`) differs from Java floating point, use `BigDecimal`
4. **CICS = synchronous transaction** — maps to a synchronous REST endpoint or a queued command
5. **Batch JCL job** — maps to a Spring Batch job with `ItemReader` / `ItemProcessor` / `ItemWriter`
6. **VSAM KSDS** — maps to a relational table or key-value store depending on access pattern
7. Always produce a **semantic risk matrix**: what was preserved, what changed, what requires human validation

---

## Test Conventions

When generating test artifacts for COBOL modernization:
- Write Java tests that validate the extracted business rules against known input/output pairs derived from the COBOL logic
- Use `@ParameterizedTest` with real data samples extracted from COBOL `WORKING-STORAGE` test values
- Flag any numeric precision differences between COBOL packed decimal and Java `BigDecimal` in test assertions
