# LL-001: Flyway Baseline Migration on Existing Database

**Category:** Database  
**Severity:** HIGH — blocked deployment for 4 hours  
**Captured:** 2025-01-10  
**Project Phase:** Foundation  

---

## What Happened

Flyway was added to an existing service that already had a database schema created manually (outside of Flyway). When deployed, Flyway attempted to run all migrations from V1 against the existing schema, causing constraint violations and failed deployment.

## Root Cause

Flyway tracks migrations in `flyway_schema_history`. When this table doesn't exist, Flyway assumes an empty database and runs all migrations. The existing schema had the tables already — Flyway's V1 migration tried to `CREATE TABLE` tables that already existed.

## Fix

Two options:

**Option A: Baseline approach (recommended for existing databases)**

```bash
flyway -url=jdbc:postgresql://host/db \
       -user=user -password=pass \
       baseline -baselineVersion=1 -baselineDescription="Existing schema"
```

This inserts a row into `flyway_schema_history` marking V1 as already applied. Future migrations (V2, V3, ...) run normally.

**Option B: Repair (for corrupted history)**

```bash
flyway repair
```

## Prevention

When adopting Flyway on an existing database:

1. Run `flyway baseline` BEFORE the first deployment
2. Add this to the runbook for any new environment setup
3. Include `flyway.baselineOnMigrate=true` in `application.yaml` for the first deployment only:

```yaml
spring:
  flyway:
    baseline-on-migrate: true
    baseline-version: 1
```

**Then remove the flag after the first successful migration.**

## Time Lost

4 hours (deployment rollback + diagnosis + fix + re-deploy)
