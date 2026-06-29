# 003 — Indexing Foreign Keys

**Script:** [`src/003_foreign_key.sql`](../../src/003_foreign_key.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
SQL Server automatically indexes primary keys but **not** foreign-key columns. As a result, cascade deletes and parent→child joins on the child side can trigger full table scans once the child table gets large — a classic, easy-to-miss performance cliff.

## The solution
Builds parent/child tables and demonstrates the I/O cost of cascade deletes and joins *with* and *without* a nonclustered index on the child FK column. Two helper views are provided: one to surface every FK's referential settings, and one to list FKs that are missing a supporting index.

## Key techniques
`FOREIGN KEY ... ON DELETE CASCADE`, nonclustered index on the FK column, `SET STATISTICS IO`, catalog views (`sys.foreign_keys`, `sys.foreign_key_columns`), execution-plan cost comparison.

## Trade-offs & when *not* to use it
Indexing *every* FK adds write and storage overhead. On FK columns that are rarely filtered or joined, and whose parent rows are rarely deleted, the index may never pay for itself. The "missing FK index" view is a starting point for investigation, not a mandate to index everything it returns.

---
[← 002 Indexing strategy](002-indexing-strategy.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 004 Extracting sensitive columns](004-splitting-customer-table.md)
