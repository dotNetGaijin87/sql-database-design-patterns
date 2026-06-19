# 020 — Soft Delete

**Script:** [`src/020_soft_delete.sql`](../../src/020_soft_delete.sql) · [Pattern index](../../README.md#the-26-patterns)

## The problem
Hard-deleting a customer loses history and can break audit or referential needs — but the application still issues ordinary `DELETE` statements, and you don't want to rewrite all of them at once.

## The solution
Adds a nullable `deleted_at` timestamp and an `INSTEAD OF DELETE` trigger that transparently converts a `DELETE` into an `UPDATE` setting `deleted_at`. A filtered index on non-null `deleted_at` keeps "deleted" rows cheap to find. The script makes the key consequence explicit: every "live" query must now add `WHERE deleted_at IS NULL`.

## Key techniques
A soft-delete flag (`deleted_at`), `INSTEAD OF DELETE` trigger, the `DELETED` pseudo-table, a filtered nonclustered index (`WHERE deleted_at IS NOT NULL`), query predicate changes.

## Trade-offs & when *not* to use it
This is a pattern with real teeth: every existing query must be rewritten to exclude soft-deleted rows (easy to forget — and forgetting leaks "deleted" data), unique constraints and FK cascades behave unexpectedly with rows that linger, and the table grows forever. Don't use it where true deletion is *required* — e.g. GDPR/right-to-erasure.

---
[← 019 Multi-language data & collations](019-handling-multilanguage-data.md) · [Pattern index](../../README.md#the-26-patterns) · [next → 021 Denormalization with JSON](021-denormalization-with-json.md)
