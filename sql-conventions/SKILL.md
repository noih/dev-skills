---
name: sql-conventions
description: "Use when: designing relational schemas, writing migrations, changing indexes, optimizing SQL queries, configuring connection pools, handling transactions, or making SQL database architecture decisions."
user-invocable: false
---

# SQL Conventions

These are defaults for new schema decisions. Preserve established schema contracts and choose types, keys, and indexes for the database and business requirements; do not migrate existing data solely to match these preferences.

## Naming Conventions

- **Table names**: Plural nouns, snake_case (`users`, `order_items`)
- **Column names**: snake_case (`created_at`, `user_id`)
- **Index names**: Follow the ORM/framework default. When naming manually, use `idx_{table}_{columns}` for regular indexes, `uq_{table}_{columns}` for unique indexes
- **Foreign key columns**: `{referenced_table_singular}_id` (e.g., `user_id`). When multiple columns reference the same table, use semantic names (e.g., `sender_id`, `receiver_id` instead of `user_id_1`, `user_id_2`)

## Primary Key Strategy

Use auto-increment integer (`SERIAL` / `BIGSERIAL` / `AUTO_INCREMENT`) as the default primary key. It's simple, compact, and index-friendly.

When a public-facing, non-sequential identifier is needed, prefer a separate `external_id` / `public_id` alongside an existing integer PK. Distributed ID generation or an established UUID key may justify a different choice. Non-sequential IDs do not replace authorization.

## Standard Columns

For ordinary mutable entities, use these defaults; join tables, immutable events, and other specialized tables may need different keys or timestamps:

| Column | Type | Description |
|--------|------|-------------|
| `id` | Auto-increment integer | Primary key |
| `created_at` | `BIGINT` | Unix epoch ms (13 digits) — record creation time |
| `updated_at` | `BIGINT` | Unix epoch ms (13 digits) — last modification time |

For new instant-valued fields without an established convention, I prefer `BIGINT` unix epoch milliseconds. Native types such as PostgreSQL `TIMESTAMPTZ` are also valid when required by existing schemas, date queries, or tooling. Specify units and conversion boundaries either way; epoch storage does not prevent parsing or display errors. Model calendar dates and local scheduled times according to their semantics rather than forcing them into instants.

- Set `created_at` on insert, never update it
- Set `updated_at` on every update (via application code or DB trigger)
- For human-readable debugging, convert ad-hoc: `to_timestamp(created_at / 1000.0)` (PostgreSQL; preserves milliseconds)
- `deleted_at` — only when soft delete is needed (see Soft Delete section)
- `created_by` / `updated_by` — only when audit trail is an explicit requirement

## Schema Design

- **Normalization**: Appropriate database normalization and relationship design. Denormalize only with clear justification (read-heavy aggregation, eliminating expensive joins on hot paths)
- **Data Integrity**: Database-level constraints (NOT NULL, UNIQUE, FK, CHECK) and application-level validation
- **Index Strategy**: Index optimization for query patterns; avoid over-indexing

## Soft Delete

Not every table needs soft delete — only add it when the feature explicitly requires recoverability or audit trail.

When needed, use a `deleted_at` column (`BIGINT`, unix epoch ms, nullable). `NULL` means the record exists; a value means it's deleted.

- Where supported, consider partial indexes on active records (`WHERE deleted_at IS NULL`) for relevant query patterns
- All queries on soft-deletable tables must filter `WHERE deleted_at IS NULL` by default. Implement this as a default scope/filter in the ORM or repository layer — don't rely on every query remembering to add it
- Enforce uniqueness among active rows using the database's supported mechanism. PostgreSQL example: `CREATE UNIQUE INDEX uq_users_active_email ON users (email) WHERE deleted_at IS NULL;`. Include tenant keys when uniqueness is tenant-scoped. `UNIQUE (email, deleted_at)` does **not** enforce active-row uniqueness under PostgreSQL's default NULL semantics. For other engines, verify their NULL/index behavior and use an equivalent design; test duplicate-active rejection, re-creation after deletion, and restore conflicts. See [PostgreSQL unique constraints](https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-UNIQUE-CONSTRAINTS).

## Enum & Status Storage

For new enum/status fields without an established convention, prefer **strings** (`VARCHAR`) with a CHECK constraint. Preserve existing integer or native-enum contracts unless a migration is justified.

- **Strings**: Self-documenting, readable in DB queries, easy to debug. Values must be in English (matches the API layer's data values convention)
- **Integers**: Compact, but require a stable mapping to remain understandable
- **DB enum types**: Can enforce a shared domain, but changing the allowed values has engine/version-specific migration constraints

```sql
-- Prefer this
status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'active', 'suspended'))

-- Alternative when the project uses a native enum
status my_status_enum NOT NULL DEFAULT 'pending'
```

When the set of valid values changes, migrate the constraint or type together with compatible application changes.

## Migration Management

- **Versioned Migrations**: Put schema changes in versioned migrations with an explicit recovery plan
- **Recovery**: Prefer reversible changes; for lossy or non-transactional operations, document backup/restore or forward recovery instead of claiming an unsafe rollback
- **Atomic Commits**: Keep migrations with dependent code when needed for a coherent, usable revision; split independently deployable changes when that improves review and rollout

## Query Optimization

- **Efficient Queries**: Use appropriate joins, avoid N+1 queries, leverage indexes
- **Parameterized Queries**: Always use parameterized queries or ORM; never interpolate user input into SQL
- **Pagination**: Prefer cursor-based pagination for large datasets; use offset-based only when UI requires arbitrary page jumps

## Connection Management

- **Connection Pooling**: Configure pool sizes based on expected load and database limits
- **Timeout Configuration**: Set appropriate connection and query timeouts
- **Health Checks**: Monitor pool utilization and connection health

## Concurrency Control

- **Lock Strategy**: Choose by the invariant: optimistic version checks with conflict handling, row locks when concurrent updates must wait, and broader locks only when required. `SKIP LOCKED` skips locked rows; use it for queue-like work where skipping is intended, not as a generally cheaper substitute for `FOR UPDATE`. See [PostgreSQL locking clauses](https://www.postgresql.org/docs/current/sql-select.html#SQL-FOR-UPDATE-SHARE).
- **Transaction Scope**: Use the smallest transaction possible. Larger transactions increase lock contention and reduce throughput. Don't wrap unrelated operations in a single transaction
