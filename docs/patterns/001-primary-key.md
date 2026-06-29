# 001 — Natural vs. Surrogate Keys

**Script:** [`src/001_primary_key.sql`](../../src/001_primary_key.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
The product is referenced everywhere by a surrogate `product_id`, but the business already has a unique `product_number`. Should the primary key be the meaningless auto-increment integer, or the meaningful business key? This script works through actually switching to the natural key without breaking any child tables.

## The solution
Propagates `product_number` into `order_item`, `review` and `review_image`; drops the old surrogate-based foreign keys; repoints the product primary key and all FKs to `product_number`; drops the redundant `product_id` columns; and finally renames `product_number` back to `product_id`. Nonclustered indexes are added on the new FK columns.

## Key techniques
`ALTER TABLE ADD/DROP COLUMN`, `DROP`/`ADD CONSTRAINT`, composite PK redefinition, `sp_rename`, `UPDATE ... FROM` with joins, nonclustered indexes on FK columns.

## Trade-offs & when *not* to use it
Natural keys propagate a wider value (a `VARCHAR(7)` vs. an `INT`) into every child table *and* every clustered index, increasing storage and index size. And if the supposedly immutable business key ever changes, you face cascading updates across the whole database. A stable surrogate key is often the safer default — the value here is understanding the trade-off, not blindly preferring either.

---
[← 000 Initial schema](000-initial-schema.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 002 Indexing strategy](002-indexing-strategy.md)
