---
name: backend-database
description: Design database schemas, write migrations, and optimize slow queries following normalization, indexing, and constraint best practices.
---
# Skill: Database Design & Query Optimization

## When to use this skill
When designing database schemas, writing migrations, or optimizing slow queries.

## Schema Design Task

```xml
<task type="auto">
  <n>Design database schema for {feature/domain}</n>
  <action>
    Follow these principles:

    1. NORMALIZATION
       - Start at 3NF: no repeating groups, no partial/transitive dependencies
       - Denormalize intentionally for read performance (document why)
       - Use junction tables for many-to-many relationships

    2. DATA TYPES
       - Use the smallest type that fits: INT vs BIGINT, VARCHAR(255) vs TEXT
       - UUIDs for public-facing IDs, auto-increment for internal PKs
       - TIMESTAMPTZ for all timestamps (never TIMESTAMP without timezone)
       - Use ENUM/CHECK constraints for known value sets
       - JSON/JSONB only for truly dynamic data — not to avoid schema design

    3. INDEXES
       - Primary key on every table (auto-created)
       - Foreign keys for all relationships (with ON DELETE behavior)
       - Index columns used in WHERE, JOIN, ORDER BY
       - Composite indexes: most selective column first
       - Partial indexes for filtered queries: WHERE status = 'active'
       - Don't over-index: each index slows writes

    4. MIGRATIONS
       - One migration per logical change
       - Always reversible (include down migration)
       - No data-destructive changes without backup confirmation
       - Add columns as nullable first, backfill, then add NOT NULL
       - Never rename columns in production — add new, migrate, drop old

    5. CONSTRAINTS
       - NOT NULL by default (nullable only when intentional)
       - UNIQUE constraints on natural keys (email, slug)
       - CHECK constraints for valid ranges
       - Foreign keys with appropriate CASCADE/RESTRICT behavior
  </action>
  <verify>Schema supports all required queries efficiently, migrations reversible</verify>
  <done>Schema designed with indexes, constraints, and migration plan</done>
</task>
```

## Query Optimization Task

```xml
<task type="auto">
  <n>Optimize slow query in {file/endpoint}</n>
  <action>
    1. Get the query execution plan: EXPLAIN ANALYZE {query}
    2. Identify bottlenecks:
       - Sequential scans on large tables → add index
       - Nested loops on large joins → check join conditions and indexes
       - Sort operations → add index matching ORDER BY
       - High row estimates vs actual → ANALYZE the table
    3. Common fixes:
       - Add missing index for WHERE/JOIN/ORDER BY columns
       - Rewrite N+1 queries as JOINs or subqueries
       - Add LIMIT to prevent unbounded results
       - Use EXISTS instead of IN for subqueries
       - Batch operations instead of row-by-row
    4. Verify: EXPLAIN ANALYZE after fix shows improvement
  </action>
  <verify>Query time reduced, EXPLAIN shows index usage</verify>
  <done>Query optimized with before/after EXPLAIN ANALYZE comparison</done>
</task>
```

## Anti-Patterns to Flag

- `SELECT *` in production queries
- Missing indexes on foreign keys
- N+1 queries in loops (ORM lazy loading)
- Unbounded queries without LIMIT
- String concatenation in SQL (injection risk)
- Storing money as FLOAT (use DECIMAL/NUMERIC)
- Missing created_at/updated_at timestamps
