# 002 — Indexing Strategy

**Script:** [`src/002_indexing_strategy.sql`](../../src/002_indexing_strategy.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
"Add an index" is the universal advice — but *which* index, on *which* columns, in *which* order, actually makes a given query fast? Developers need to see how clustered-key choice, column order, included columns and covering indexes map onto real query shapes: filters, joins, `ORDER BY`, `GROUP BY` and aggregates.

## The solution
A non-mutating lab that builds throwaway tables and many index variants, then compares execution plans and physical page structures across roughly a dozen scenarios — clustered-key width, `IDENTITY` gaps, cardinality, composite indexes vs. `INCLUDE`, sargability, and indexing for joins/sorts/grouping/counts/aggregates. Index hints force each plan so the cost difference is unambiguous, and DMVs surface duplicate and unused indexes.

## Key techniques
Clustered vs. nonclustered indexes, unique indexes, composite indexes, `INCLUDE` columns, key-lookup analysis, `WITH (INDEX(...))` hints, sargability, `sys.dm_db_index_usage_stats`, a `FOR XML PATH` duplicate-index CTE.

## Trade-offs & when *not* to use it
Every additional index costs storage and slows down `INSERT` / `UPDATE` / `DELETE`. Over-indexing — and especially duplicate or overlapping indexes — quietly degrades write throughput. Indexes should match the *real* workload, not be added speculatively because a column "looks searchable."

---
[← 001 Natural vs. surrogate keys](001-primary-key.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 003 Indexing foreign keys](003-foreign-key.md)
