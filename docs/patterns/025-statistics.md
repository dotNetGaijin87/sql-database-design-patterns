# 025 — Query-Optimizer Statistics

**Script:** [`src/025_statistics.sql`](../../src/025_statistics.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
The optimizer's row estimates — and therefore plan quality and memory grants — depend entirely on **statistics**. When data is skewed or histograms go stale, the optimizer guesses wrong and you get scans, bad join choices, and bloated memory grants. To fix a bad plan, you first have to be able to *read* why the optimizer chose it.

## The solution
Inspects statistics objects (the histogram and the density vector), builds a deliberately skewed table to show **filtered statistics** improving estimates on a sub-range, walks through how the optimizer uses `EQ_ROWS`/`AVG_RANGE_ROWS` from the histogram versus the density vector (constant vs. parameterized predicates), and finally demonstrates how disabling auto-update and then deleting half the rows produces outdated stats and an excessive memory grant — detectable via DMVs.

## Key techniques
`DBCC SHOW_STATISTICS` (histogram / density), `CREATE STATISTICS ... WHERE ... WITH FULLSCAN` (filtered stats), histogram `EQ_ROWS` vs. `AVG_RANGE_ROWS`, density-vector estimation, `STATISTICS_NORECOMPUTE`, `sys.dm_exec_query_stats` memory-grant analysis.

## Trade-offs & when *not* to use it
Filtered and manually-maintained statistics add maintenance overhead and can themselves go stale. Disabling `AUTO_UPDATE_STATISTICS` (or relying on parameterized predicates that fall back to the density vector) leads to poor estimates. Manual statistics management should be reserved for known skew or very large tables — for everything else, let SQL Server manage stats automatically.

---
[← 024 Numbers/calendar tables & indexed views](024-precalculated-tables-and-indexed-views.md) · [Pattern index](../../README.md#the-25-patterns)
