---
name: sql-conventions
description: SQL schema, migration, query, indexing, connection, and transaction conventions. Use when designing relational database schemas, writing migrations, optimizing SQL queries, configuring connection pools, or making SQL database architecture decisions.
user-invocable: false
---

# SQL Conventions

## Naming Conventions

- **Table names**: Plural nouns, snake_case (`users`, `order_items`)
- **Column names**: snake_case (`created_at`, `user_id`)
- **Index names**: Follow the ORM/framework default. When naming manually, use `idx_{table}_{columns}` for regular indexes, `uq_{table}_{columns}` for unique indexes
- **Foreign key columns**: `{referenced_table_singular}_id` (e.g., `user_id`). When multiple columns reference the same table, use semantic names (e.g., `sender_id`, `receiver_id` instead of `user_id_1`, `user_id_2`)

## Primary Key Strategy

Use auto-increment integer (`SERIAL` / `BIGSERIAL` / `AUTO_INCREMENT`) as the default primary key. It's simple, compact, and index-friendly.

When a public-facing, non-sequential identifier is needed (e.g., for URLs, external APIs, or preventing enumeration), add a separate column (`external_id`, `public_id`, etc.) — do not replace the auto-increment PK. This keeps internal joins and indexes on efficient integers while exposing a safe identifier externally.

## Standard Columns

Every table should include:

| Column | Type | Description |
|--------|------|-------------|
| `id` | Auto-increment integer | Primary key |
| `created_at` | `BIGINT` | Unix epoch ms (13 digits) — record creation time |
| `updated_at` | `BIGINT` | Unix epoch ms (13 digits) — last modification time |

Store timestamps as `BIGINT` (unix epoch milliseconds), not native `TIMESTAMP`/`TIMESTAMPTZ`. This eliminates the timezone conversion chain (app server → ORM → DB session → DB server) that silently introduces bugs in cross-timezone services. A number has no timezone to convert.

- Set `created_at` on insert, never update it
- Set `updated_at` on every update (via application code or DB trigger)
- For human-readable debugging, convert ad-hoc: `to_timestamp(created_at / 1000)` (PostgreSQL)
- `deleted_at` — only when soft delete is needed (see Soft Delete section)
- `created_by` / `updated_by` — only when audit trail is an explicit requirement

## Schema Design

- **Normalization**: Appropriate database normalization and relationship design. Denormalize only with clear justification (read-heavy aggregation, eliminating expensive joins on hot paths)
- **Data Integrity**: Database-level constraints (NOT NULL, UNIQUE, FK, CHECK) and application-level validation
- **Index Strategy**: Index optimization for query patterns; avoid over-indexing

## Soft Delete

Not every table needs soft delete — only add it when the feature explicitly requires recoverability or audit trail.

When needed, use a `deleted_at` column (`BIGINT`, unix epoch ms, nullable). `NULL` means the record exists; a value means it's deleted.

- Add a partial index on active records (e.g., `WHERE deleted_at IS NULL`) so queries on live data don't scan deleted rows
- All queries on soft-deletable tables must filter `WHERE deleted_at IS NULL` by default. Implement this as a default scope/filter in the ORM or repository layer — don't rely on every query remembering to add it
- UNIQUE constraints must include `deleted_at` to allow re-creation after deletion (e.g., `UNIQUE (email, deleted_at)`)

## Enum & Status Storage

Store enum/status values as **strings** (`VARCHAR`), not integers or DB enum types.

- **Strings**: Self-documenting, readable in DB queries, easy to debug. Values must be in English (matches the API layer's data values convention)
- **Not integers**: Integers require a lookup table or comments to understand. The storage savings are negligible for status fields
- **Not DB enum types**: `ALTER TYPE` is painful — PostgreSQL can't remove values, and adding values had transaction restrictions before PG12. Use CHECK constraints or application-level validation instead

```sql
-- Prefer this
status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'active', 'suspended'))

-- Not this
status my_status_enum NOT NULL DEFAULT 'pending'
```

When the set of valid values changes, update the CHECK constraint in a migration — simpler and more portable than altering DB enum types.

## Migration Management

- **Versioned Migrations**: Every schema change through versioned migration files with rollback support
- **Atomic Migrations**: Each migration should be self-contained and reversible
- **Separate Commits**: Database migrations deserve their own commit

## Query Optimization

- **Efficient Queries**: Use appropriate joins, avoid N+1 queries, leverage indexes
- **Parameterized Queries**: Always use parameterized queries or ORM; never interpolate user input into SQL
- **Pagination**: Prefer cursor-based pagination for large datasets; use offset-based only when UI requires arbitrary page jumps

## Connection Management

- **Connection Pooling**: Configure pool sizes based on expected load and database limits
- **Timeout Configuration**: Set appropriate connection and query timeouts
- **Health Checks**: Monitor pool utilization and connection health

## Concurrency Control

- **Lock Strategy**: Prefer the lightest lock that fits: Optimistic Locking > SKIP LOCKED > Row Lock (FOR UPDATE) > Page Lock > Table Lock. Avoid unnecessary heavy locks
- **Transaction Scope**: Use the smallest transaction possible. Larger transactions increase lock contention and reduce throughput. Don't wrap unrelated operations in a single transaction
