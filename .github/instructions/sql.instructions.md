---
applyTo: "**/*.sql, **/mapper/**/*.xml, **/mappers/**/*.xml, **/repository/**/*.xml"
---

## Context

This instruction file applies to SQL files and MyBatis XML mapper files. The primary database platform is **IBM DB2** (both z/OS and Linux/Unix/Windows variants). SQL must be compatible with DB2 dialect. Additional awareness of embedded SQL in COBOL programs (via `EXEC SQL ... END-EXEC` blocks) also applies. Security and correctness of queries is paramount — SQL injection via string concatenation is a critical vulnerability that must never appear.

---

## Coding Standards

- **Always qualify table names** with the schema prefix: `SCHEMA.TABLE_NAME` — never bare table references
- **Parameterized queries only** — never concatenate user input into SQL strings
- **No `SELECT *`** — always enumerate required columns explicitly
- **Row limiting:** Use DB2 syntax `FETCH FIRST n ROWS ONLY` — never `ROWNUM` (Oracle) or `LIMIT` (MySQL)
- **Upserts:** Prefer `MERGE` statement over separate `INSERT` / `UPDATE` logic
- **Index hints:** Only with a DBA approval comment explaining the rationale
- **Schema migration scripts:** Use sequential versioned names (`V001__description.sql`) for Flyway/Liquibase
- **Transactions:** Explicit `COMMIT` / `ROLLBACK` in batch scripts; in Java, defer to Spring `@Transactional`
- **NULL handling:** Always account for nullable columns — use `COALESCE` or explicit `NULL` checks

---

## Preferred Patterns

### Parameterized Query (JDBC)

```java
// CORRECT: PreparedStatement with named parameters via NamedParameterJdbcTemplate
String sql = "SELECT c.customer_id, c.customer_name, c.balance " +
             "FROM SCHEMA.CUSTOMERS c " +
             "WHERE c.customer_id = :customerId " +
             "AND c.status = :status";

MapSqlParameterSource params = new MapSqlParameterSource()
    .addValue("customerId", customerId)
    .addValue("status", status.getCode());

return jdbc.queryForObject(sql, params, new CustomerRowMapper());
```

### DB2-Compliant SELECT with FETCH FIRST

```sql
-- Paginated query with DB2 row limiting syntax
SELECT
    c.customer_id,
    c.customer_name,
    c.email_address,
    c.created_timestamp
FROM
    SCHEMA.CUSTOMERS c
WHERE
    c.status = 'ACTIVE'
    AND c.created_timestamp >= :cutoffDate
ORDER BY
    c.customer_name ASC
FETCH FIRST 100 ROWS ONLY
```

### MERGE for Upsert

```sql
MERGE INTO SCHEMA.CUSTOMER_PREFERENCES AS target
USING (VALUES (:customerId, :preferenceKey, :preferenceValue))
    AS source (customer_id, pref_key, pref_value)
ON (target.customer_id = source.customer_id
    AND target.pref_key = source.pref_key)
WHEN MATCHED THEN
    UPDATE SET
        pref_value = source.pref_value,
        updated_timestamp = CURRENT TIMESTAMP
WHEN NOT MATCHED THEN
    INSERT (customer_id, pref_key, pref_value, created_timestamp)
    VALUES (source.customer_id, source.pref_key, source.pref_value, CURRENT TIMESTAMP)
```

### DB2 Window Function

```sql
SELECT
    o.order_id,
    o.customer_id,
    o.order_amount,
    SUM(o.order_amount) OVER (
        PARTITION BY o.customer_id
        ORDER BY o.created_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total
FROM
    SCHEMA.ORDERS o
WHERE
    o.status = 'COMPLETED'
```

### MyBatis XML Mapper

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.repository.CustomerMapper">

    <!-- resultMap preferred over inline resultType for complex mappings -->
    <resultMap id="customerResultMap" type="com.example.domain.Customer">
        <id property="id" column="customer_id"/>
        <result property="name" column="customer_name"/>
        <result property="email" column="email_address"/>
        <result property="balance" column="balance"/>
        <result property="status" column="status" typeHandler="com.example.handler.StatusTypeHandler"/>
        <association property="address" resultMap="addressResultMap"/>
    </resultMap>

    <select id="findById" parameterType="long" resultMap="customerResultMap">
        SELECT
            c.customer_id,
            c.customer_name,
            c.email_address,
            c.balance,
            c.status,
            a.street,
            a.city,
            a.postal_code
        FROM
            SCHEMA.CUSTOMERS c
            LEFT JOIN SCHEMA.ADDRESSES a ON a.customer_id = c.customer_id
        WHERE
            c.customer_id = #{id}
        FETCH FIRST 1 ROWS ONLY
    </select>

    <select id="findByStatus" resultMap="customerResultMap">
        SELECT
            c.customer_id,
            c.customer_name,
            c.email_address,
            c.balance,
            c.status
        FROM
            SCHEMA.CUSTOMERS c
        WHERE
            c.status = #{status}
        <if test="createdAfter != null">
            AND c.created_timestamp >= #{createdAfter}
        </if>
        ORDER BY c.customer_name ASC
    </select>

    <insert id="insert" parameterType="com.example.domain.Customer">
        INSERT INTO SCHEMA.CUSTOMERS
            (customer_id, customer_name, email_address, balance, status, created_timestamp)
        VALUES
            (#{id}, #{name}, #{email}, #{balance}, #{status}, CURRENT TIMESTAMP)
    </insert>

</mapper>
```

### DB2 Embedded SQL (COBOL context)

```cobol
      * CORRECT: Host variables with proper SQLCODE check
           MOVE WS-INPUT-ID TO HV-CUSTOMER-ID
           EXEC SQL
               SELECT CUSTOMER_NAME,
                      BALANCE,
                      STATUS
               INTO   :HV-CUST-NAME,
                      :HV-BALANCE,
                      :HV-STATUS
               FROM   SCHEMA.CUSTOMERS
               WHERE  CUSTOMER_ID = :HV-CUSTOMER-ID
           END-EXEC
           EVALUATE SQLCODE
               WHEN 0
                   CONTINUE
               WHEN 100
                   MOVE 'NOT FOUND' TO WS-ERROR-MSG
               WHEN OTHER
                   MOVE SQLCODE TO WS-SQLCODE
                   PERFORM 9900-SQL-ERROR
           END-EVALUATE.
```

---

## Anti-Patterns — Do NOT Generate

```java
// WRONG: string concatenation in SQL — SQL injection vulnerability [BLOCKER]
String sql = "SELECT * FROM CUSTOMERS WHERE ID = " + customerId;
Statement stmt = conn.createStatement();
stmt.execute(sql);

// WRONG: SELECT * — fetches unnecessary columns, breaks on schema changes
"SELECT * FROM SCHEMA.ORDERS WHERE status = :status"

// WRONG: ROWNUM syntax (Oracle) — DB2 uses FETCH FIRST
"SELECT id FROM SCHEMA.ORDERS WHERE ROWNUM <= 10"

// WRONG: LIMIT syntax (MySQL/PostgreSQL) — DB2 uses FETCH FIRST
"SELECT id FROM SCHEMA.ORDERS LIMIT 10"

// WRONG: unqualified table name — breaks cross-schema deployments
"SELECT id FROM ORDERS WHERE status = :status"

// WRONG: inline resultType for complex mappings — use resultMap
<select id="find" resultType="com.example.Customer">
```

```sql
-- WRONG: non-parameterized dynamic SQL in a stored procedure
SET V_SQL = 'SELECT * FROM ' || V_TABLE_NAME || ' WHERE ID = ' || V_INPUT_ID;
-- Use parameterized EXECUTE IMMEDIATE with USING clause instead
```

---

## Dependencies & Versions

| Technology | Version | Notes |
|-----------|---------|-------|
| DB2 | 12.x (z/OS) / 11.x (LUW) | Use DB2-specific syntax for row limiting, window functions |
| MyBatis | 3.5.x | XML mapper format documented above |
| Spring JDBC | (with Spring) | `NamedParameterJdbcTemplate` preferred over plain `JdbcTemplate` |
| Flyway | 9.x+ | Migration scripts: `V{version}__{description}.sql` |
| Liquibase | 4.x+ | Alternative migration tool — YAML or XML changesets |

---

## Test Conventions

- Test SQL and MyBatis mappers with `@DataJpaTest` (JPA) or an embedded H2 database (JdbcTemplate/MyBatis)
- When H2 compatibility mode is insufficient for DB2 syntax, use Testcontainers with `ibmcom/db2` image
- Verify both the happy path (row found) and not-found cases for every query
- Test `MERGE` statements by setting up pre-existing rows and verifying both insert and update branches
- Validate that pagination queries respect `FETCH FIRST` limits and return the correct page
