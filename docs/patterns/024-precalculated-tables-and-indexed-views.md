# 024 — Numbers/Calendar Tables & Indexed Views

**Script:** [`src/024_precalculated_tables_and_indexed_views.sql`](../../src/024_precalculated_tables_and_indexed_views.sql) · [Pattern index](../../README.md#the-26-patterns)

## The problem
Gap-finding, date-series generation, working-day / payday / shipment-date logic, and repeated expensive aggregations are all slow or awkward to compute query-by-query.

## The solution
Builds three precalculation tools:
- A persisted **numbers table** (1–100,000) for set-based gap detection and date generation.
- A rich **calendar table** with weekend / holiday / payday / end-of-month flags for business-day logic.
- A `SCHEMABINDING` **indexed view** with a unique clustered index that materializes per-customer/product spend — benchmarked against an equivalent ordinary view for both read and write cost.

## Key techniques
Numbers table, recursive CTE with `MAXRECURSION 0`, calendar/date dimension, `EOMONTH`/`DATEFROMPARTS`/`DATEPART`, `CROSS APPLY` for next-business-day, indexed view (`WITH SCHEMABINDING` + unique clustered index + `COUNT_BIG`), the `NOEXPAND` hint, the required `SET` options.

## Trade-offs & when *not* to use it
An indexed view is materialized, so it must be updated on **every** write to its base tables — the script measures exactly that cost. It also carries strict `SET`-option and `SCHEMABINDING` requirements and locks the underlying schema against change. Indexed views pay off only for read-heavy, infrequently-changing aggregates; on write-heavy tables they're a net loss.

---
[← 023 Sensitive-data protection](023-sensitive-data-obfuscation.md) · [Pattern index](../../README.md#the-26-patterns) · [next → 025 Query-optimizer statistics](025-statistics.md)
