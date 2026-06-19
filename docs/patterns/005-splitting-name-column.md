# 005 — Zero-Downtime Column Split

**Script:** [`src/005_splitting_name_column.sql`](../../src/005_splitting_name_column.sql) · [Pattern index](../../README.md#the-26-patterns)

## The problem
A single `name` column can't be reliably queried or sorted by first vs. last name. Splitting it sounds trivial — until you remember **live clients are still reading and writing the old column** throughout the rollout. You can't take the app down, and you can't break old code that hasn't been deployed yet.

## The solution
Adds `first_name` / `last_name` columns and an `INSTEAD OF INSERT/UPDATE` trigger that **bidirectionally syncs** the old `name` column with the new ones, so old and new application code can coexist during the transition. Once every client speaks the new schema, the trigger is dropped, the data is backfilled in batches, and the old column is removed. This is the expand/contract (parallel-change) migration pattern.

## Key techniques
`INSTEAD OF` trigger, `INSERTED`/`DELETED` pseudo-tables, `SUBSTRING`/`CHARINDEX` parsing, `ISNULL` coalescing, a batched `UPDATE` loop driven by `@@ROWCOUNT`, `TRY/CATCH`, `IDENTITY_INSERT`.

## Trade-offs & when *not* to use it
The dual-write trigger adds per-row overhead and real complexity for the duration of the migration window, and naive name-splitting is fragile on single-word or multi-word names. Only reach for this when you genuinely have a **zero-downtime requirement**; for a maintenance-window migration, a simple backfill is far less risky.

---
[← 004 Extracting sensitive columns](004-splitting-customer-table.md) · [Pattern index](../../README.md#the-26-patterns) · [next → 006 Data cleansing & format enforcement](006-simple-format.md)
