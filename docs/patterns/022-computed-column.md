# 022 — Computed Columns & Maintained Aggregates

**Script:** [`src/022_computed_column.sql`](../../src/022_computed_column.sql) · [Pattern index](../../README.md#the-26-patterns)

## The problem
Computing each product's average rating on the fly — and then filtering or sorting by it — is expensive when the reviews live in JSON ([021](021-denormalization-with-json.md)). And the value is read far more often than it changes.

## The solution
Adds maintained `number_of_reviews` and `sum_of_ratings` counters plus a `PERSISTED` computed `average_rating` column (with an index on it), backfills them from the JSON reviews, and adds an `AFTER INSERT/UPDATE/DELETE` trigger on `product_review` that keeps the counters in sync **incrementally** (applying deltas rather than recomputing from scratch).

## Key techniques
`PERSISTED` computed column, index on a computed column, `NULLIF` divide-by-zero guard, `AFTER` trigger with incremental aggregate maintenance, `JSON_VALUE`, `INSERTED`/`DELETED` deltas.

## Trade-offs & when *not* to use it
The trigger adds write overhead and is a genuine correctness risk: the counters can drift if rows are ever modified outside the trigger, or under concurrency. A `PERSISTED` indexed computed column also costs storage and write maintenance. Not worth it for aggregates that are read rarely and could simply be computed on demand.

---
[← 021 Denormalization with JSON](021-denormalization-with-json.md) · [Pattern index](../../README.md#the-26-patterns) · [next → 023 Sensitive-data protection](023-sensitive-data-obfuscation.md)
