
## Liquibase & SQL skill (SKILL.md)

The repo includes a **Liquibase & SQL** skill for Cursor/AI-assisted work on schema and migrations.

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Modulr Liquibase and SQL practices: schema management, BID/ID strategy, DDL/DML/DCL, changelog design, and Always/Never rules. |

**When it applies:** Writing or reviewing Liquibase changesets, designing schemas, adding tables/columns/indexes, planning data migrations, or working with service databases.

**Quick reference:**
- **Always:** Idempotent changesets, same statements/order everywhere, CONCURRENTLY for standalone index changesets, table-prefixed columns, roll forward.
- **Never:** Edit applied changesets, use both ID and BID on a new table, lock tables unnecessarily, use IDs in `WHERE` in migrations.