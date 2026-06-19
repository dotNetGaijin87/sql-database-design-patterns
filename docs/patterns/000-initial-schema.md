# 000 — Initial Schema & Diagnostic Tooling

**Script:** [`src/000_initial_schema_creation.sql`](../../src/000_initial_schema_creation.sql) · [Pattern index](../../README.en.md#the-26-patterns)

## The problem
A greenfield e-commerce app needs a starting relational schema — and, just as importantly, the introspection tooling that every later refactoring lesson relies on to *prove* an improvement actually happened.

## The solution
Creates the `OnlineStore` database with `person` / `sales` / `production` schemas and the core tables (customer, product, product_category, order, order_item, order_shipment, review, review_image), wired together with primary keys, foreign keys and check constraints. It also creates a `migration_history` audit table and a suite of diagnostic stored procedures and views: page/filegroup inspection, missing-FK-index detection, and live lock information.

The schema is **deliberately imperfect** — it embeds anti-patterns (a denormalized customer row, free-text status, a comma-separated `wishlist`) so the later scripts have something real to fix.

## Key techniques
`CREATE DATABASE` / `CREATE SCHEMA`, `IDENTITY` columns, composite primary keys, foreign keys with `ON DELETE CASCADE`, check constraints, a self-referencing FK, `DBCC PAGE`, `sys.dm_db_index_physical_stats`, `sys.dm_db_database_page_allocations`, `sys.dm_tran_locks`, `STRING_SPLIT`, catalog views.

## Trade-offs & when *not* to use it
The diagnostic procedures use `DBCC PAGE` and `DBCC DROPCLEANBUFFERS`, which are undocumented and/or disruptive — great for a learning lab, unsuitable for a production server. The embedded anti-patterns are intentional teaching scaffolding, not a recommended starting design.

---
[Pattern index](../../README.en.md#the-26-patterns) · [next → 001 Natural vs. surrogate keys](001-primary-key.md)
