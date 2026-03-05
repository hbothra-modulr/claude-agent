---
name: liquibase-sql
description: Apply Modulr Liquibase and SQL practices for schema management, migrations, BID/ID strategy, DDL/DML/DCL, and changelog design. Use when writing or reviewing Liquibase changesets, designing schemas, adding tables/columns/indexes, planning data migrations, or working with service databases.
---
# Liquibase & SQL – Modulr Practices
Source docs (reviewed against Confluence):
- [Guide to Liquibase](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3382411352/Guide+to+Liquibase) – fundamentals, changelog, testing, decentralised schema
- [Guide to Liquibase \| Examples](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3901784265/Guide+to+Liquibase+Examples) – schema, table, sequence, index, column, data, partitioning
- [Liquibase Best Practices](https://modulrfinance.atlassian.net/wiki/spaces/GC/pages/3987046401/Guide+to+Liquibase+Best+Practices)
- [Elevated DB migrations](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3465019431)
- [BID prefix usage](https://modulrfinance.atlassian.net/wiki/spaces/MP/pages/128450575)
---
## 1. Liquibase & Schema Management Fundamentals
- Liquibase is the schema management tool for **fully managing** service database schemas.
- Use the **schema-management** Gradle plugin to wrap Liquibase and automate deployments.
- Ensure the **exact same statements** run, in the **same order**, across all databases/environments.
- Test changesets locally using:
  - `modulo-bootstrap`’s `upgradeDb` task, or
  - [Schema Management CLI](https://git.modulrfinance.io/modtools/schema-management/tree/master/schema-management-cli)
- To re-run changesets during development: delete the relevant `databasechangelog` entry and associated business data locally.
- Always write **idempotent** changesets that can be safely applied multiple times.
---
## 2. New Schema Design
- Design new schemas using the **decentralised database** approach ([Guide to Liquibase](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3382411352)).
- Configure a **service-specific user** for each new schema (not the system-wide `mserviceuser`).
- Use **base27_encode** for new schemas (not the older `base33_encode`); BID generation uses `generate_bid` (service in its own DB) or `generate_safe_bid` (service still under modulo DB)—see [Examples](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3901784265).
---
## 3. Data Migration on Live Systems
- Plan data migration for **live services** carefully.
- Avoid **locking tables** (e.g. when dropping and recreating a view after updating column values).
- Structure migrations to minimise long-running locks and impact.
---
## 4. BID vs ID Strategy
- For **new tables**, define **either** a sequential ID **or** a BID, **not both**:
  - **ID**: when data is private to the service and never exposed outside the platform.
  - **BID**: for all other scenarios.
- ID and BID are both unique identifiers; having both can cause issues (e.g. Postgres join/ordering).
- BIDs are supplied externally so teams (e.g. PayOps) can visually verify values.
- Use **bigint** for ID column types.
- **Child tables** where the parent has both ID and BID (e.g. `modulo_account.customer`):
  - Create the FK **against the parent BID** to support future removal of dual identifiers and to use the BID’s unique index for performance.
- Long-term plan: drop IDs from tables that have both ID and BID.
---
## 5. BID Lengths & Types
- **Primary key** BIDs on new tables: **CHAR(10)**.
- **Foreign key** BIDs: **VARCHAR(10)** (to support legacy CHAR(8) and new CHAR(10)).
- BIDs created as `character varying, 7` are effectively CHAR(10): `<1 char sequence> + <2 digit database id> + <7 digits>`.
---
## 6. Changelog & Changeset Practices
- Prefer changesets that **can be rolled back**.
- **Never modify previously deployed changesets**: MD5 checksums change, comparison fails, the updated changeset is not applied, and the release can stop.
- Use comments: brief comments in `changelog.yml` are written to `databasechangelog`; verbose comments in the changeset file help maintainers but are not stored.
- Test locally; delete the relevant `databasechangelog` entry and business data to re-run.
- Use **idempotent** operations so changesets can be applied multiple times.
- Reference columns by **table name** in changesets.
- Avoid `jsonb` unless in documented scenarios (see JSONB section).
- Prefer **varchar** over `text`/`character varying`, with constrained sizes from known expected values.
- Use the **elevated changelog** for changes requiring increased permissions (see elevated DB migrations doc).
---
## 7. Roll Forward / Back Strategy
- Prefer **roll forward**: add a new changeset to revert or adjust previous changes instead of editing existing ones.
- Consider how each change can be rolled back.
- Provide **forwards/backwards compatibility over 2 releases** (e.g. remove table access in release 1, drop table in release 2).
- Do **not** remove or alter columns in active use without full consultation and alignment with Java entity expectations.
- Drop tables only in the **next release** after functionality is removed (supports release rollback).
- Avoid bespoke one-off data migrations; do **not** use IDs in `WHERE` clauses.
---
## 8. Naming Patterns & Abbreviations
- Name objects so names reflect content (e.g. index on `account_bid` → `idx_account_bid`, not `idx_bid`).
- Standard abbreviations: `idx` (index), `pk` (primary key), `fk` (foreign key), `uk` (unique key).
---
## 9. DDL (Data Definition Language)
- Check if artefacts **exist or do not exist** before applying changes: use **IF NOT EXISTS** and **IF EXISTS** where appropriate.
- Do not remove or alter columns in active use without full consultation and rollback consideration.
- Prefix table column names with the **table name abbreviation**.
- **Sequence naming**: include the table name in the sequence name to avoid conflicts (e.g. `<my_table_seq>` for table `my_table`); see [Examples](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3901784265).
### 9.1 Index Management
- Create indexes with **CREATE INDEX CONCURRENTLY** to avoid locks that block concurrent inserts/updates/deletes.
- **Omit CONCURRENTLY** when creating the index in the **same changeset as the table** is being created (per [Guide to Liquibase \| Examples](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3901784265)).
- Index-only changesets: changeset file contains **only index statements**; `changelog.yml` entry has **runInTransaction: false**.
### 9.2 Column Conventions
- **snake_case**; identity columns: `id_<table_name>` (e.g. `id_my_table`).
- Column names: table prefix + column name (e.g. table `my_table` → `mt_my_column`).
- Use standard base classes for common columns: `BaseDeleteableEntity`, `BaseImmutableEntity`, `BaseUpdateableEntity` (not deprecated `BaseEntity`).
- Include standard audit columns (e.g. `row_create_dt`) where appropriate.
- Consistent types: **VARCHAR** not `CHARACTER VARYING`, **CHAR** not `CHARACTER`.
---
## 10. DQL & DML
- Put data changes in a **separate changeset** with **runInTransaction: true** when all changes must roll back together if any fail.
- Use **idempotent** data operations where possible (e.g. `INSERT ... ON CONFLICT DO NOTHING` per [Examples](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3901784265)).
- Apply **limits** to queries to avoid long-running locks, especially when changing many rows in large tables.
- Avoid **SELECT ... WHERE x IN (list)** when list size exceeds **Short.MAX_VALUE** (prevents “Tried to send an out-of-range integer as a 2-byte value”).
---
## 11. DCL (Permissions & Roles)
- Create `domo` and `metabase` users on new database hosts when required.
- Assign **least permissive** roles and permissions.
- Do not revoke a role only to reassign it; assign the required role directly.
- For views: grant **GRANT SELECT** only.
---
## 12. JSONB Column Usage
- Use JSONB **only** for:
  - Unstructured/schemaless data that does not need to be queried, or
  - Temporary storage for JSON from external systems before ingestion.
- Avoid JSONB for queryable/structured data: harder to search, only simple top-level key existence, type ambiguity, harder to modify, no FK constraints, no statistics.
- Combine JSONB with regular columns: promote frequently used keys to proper columns; use JSONB as a **catch-all** for infrequent or extra fields.
- If data is queried, represent its structure in **tables**, not only in JSONB.
---
## 13. Users, Roles & Permissions for Liquibase-Managed Schemas
- For Liquibase-managed services, create a **service-specific user** for schema access (not legacy `mserviceuser`).
- Use the **&lt;service user&gt;service** pattern for new schemas.
- List service role names in Postgres:
  ```sql
  SELECT rolname
  FROM pg_roles
  WHERE LOWER(rolname) LIKE '%service%'
  ORDER BY rolname;
  ```

## 14. Partitioning & Other Topics
- **Partitioned tables**, partman extension, and partitioning config (e.g. `runAsElevated: true` for pg_partman) are documented in [Guide to Liquibase \| Examples](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/3901784265) (Partitioning section).
- Use **COMMENT ON TABLE** / **COMMENT ON COLUMN** where they help future readers (Examples show this pattern).

---

## Always Rules

- **Always** write idempotent changesets that can be safely applied multiple times.
- **Always** ensure the exact same statements run in the same order across all databases/environments.
- **Always** use the schema-management Gradle plugin to wrap Liquibase and automate deployments.
- **Always** design new schemas using the decentralised database approach.
- **Always** configure a service-specific user for each new schema (not `mserviceuser`).
- **Always** use base27_encode for new schemas.
- **Always** use bigint for ID column types.
- **Always** create child-table FKs against the parent BID when the parent has both ID and BID.
- **Always** prefer changesets that can be rolled back.
- **Always** reference columns by table name in changesets.
- **Always** use IF NOT EXISTS / IF EXISTS when applying DDL changes.
- **Always** prefix table column names with the table name abbreviation.
- **Always** use CREATE INDEX CONCURRENTLY for indexes in **standalone** changesets; omit CONCURRENTLY when the index is created in the same changeset as the table.
- **Always** put index-only statements in their own changeset and set `runInTransaction: false` in changelog.yml.
- **Always** use snake_case; identity columns as `id_<table_name>`.
- **Always** use standard base classes (BaseDeleteableEntity, BaseImmutableEntity, BaseUpdateableEntity) for common columns.
- **Always** use VARCHAR (with constrained sizes) and CHAR in DDL; avoid `text` / `character varying` / `character` where a size is known.
- **Always** put data changes in a separate changeset with runInTransaction: true when changes must roll back together.
- **Always** apply limits to queries that affect many rows in large tables.
- **Always** assign the least permissive roles and permissions (DCL).
- **Always** use the elevated changelog for changes that require increased permissions.
- **Always** provide forwards/backwards compatibility over 2 releases when removing or changing schema.
- **Always** drop tables only in the next release after functionality is removed.
- **Always** name objects so the name reflects content (e.g. `idx_account_bid` for an index on `account_bid`).
- **Always** use standard abbreviations: idx, pk, fk, uk.
- **Always** create domo and metabase users on new database hosts when required.
- **Always** grant only SELECT on views.

---

## Never Rules

- **Never** modify previously deployed changesets (MD5 changes, comparison fails, release can stop).
- **Never** define both a sequential ID and a BID on the same new table; use one or the other.
- **Never** use the system-wide `mserviceuser` for new schemas; use a service-specific user.
- **Never** use base33_encode for new schemas.
- **Never** lock tables unnecessarily (e.g. drop/create view in one step when updating column values).
- **Never** remove or alter columns in active use without full consultation and alignment with Java entities.
- **Never** use IDs in WHERE clauses in data migrations (avoids bespoke one-off migrations).
- **Never** use SELECT ... WHERE x IN (list) when list size exceeds Short.MAX_VALUE.
- **Never** use jsonb for queryable or structured data; only for non-queryable/unstructured or temporary pre-ingestion storage.
- **Never** represent queryable data only in JSONB; use proper tables/columns.
- **Never** revoke a role only to reassign it; assign the required role directly.
- **Never** use deprecated BaseEntity; use the standard base entities.
- **Never** put non-index DDL in the same changeset as CREATE INDEX CONCURRENTLY; keep index changesets index-only.
- **Never** use CONCURRENTLY when creating an index in the same changeset as the table; omit CONCURRENTLY in that case.

---
