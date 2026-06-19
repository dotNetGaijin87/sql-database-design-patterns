# 009 — Enum → Lookup Table

**Script:** [`src/009_order_status_type_lookup_table.sql`](../../src/009_order_status_type_lookup_table.sql) · [Pattern index](../../README.md#the-26-patterns)

## The problem
Order status is stored as a free-text string on every order. That wastes space, allows typos, and gives you no referential control over which statuses are even valid.

## The solution
Creates a `sales.order_status_type` lookup table (a compact `TINYINT` key) seeded with the valid statuses, adds `order_status_type_id` to `order`, backfills it by joining on the old string, enforces it with a foreign key, and drops the old `status` column.

## Key techniques
`TINYINT IDENTITY` surrogate key, lookup seeding, `UPDATE ... FROM` join, `FOREIGN KEY WITH CHECK` and `ON UPDATE CASCADE`, `ALTER TABLE DROP COLUMN`.

## Trade-offs & when *not* to use it
Every status read now needs a join to resolve the human-readable name, and adding a status becomes a data insert rather than a code change. This normalization is overkill for a status set that never changes and is only ever displayed via application-side constants — in that case an enum in code (or a `CHECK` constraint) is simpler.

---
[← 008 Reference table](008-state-lookup-table.md) · [Pattern index](../../README.md#the-26-patterns) · [next → 010 Many-to-many](010-associative-table.md)
