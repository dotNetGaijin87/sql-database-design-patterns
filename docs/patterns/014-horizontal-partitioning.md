# 014 — Horizontal (Range) Partitioning & Sliding Window

**Script:** [`src/014_horizontal_partitioning.sql`](../../src/014_horizontal_partitioning.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
A large time-series `order_report` table needs to **archive last year's data and load this year's instantly**, and to keep date-range scans fast — without rewriting the whole table every time.

## The solution
Creates per-year filegroups and files, a `RANGE RIGHT` partition function and scheme on `order_date`, and a partitioned table. It then demonstrates the **sliding-window** pattern: `SPLIT`/`MERGE` of the partition function plus near-instant `SWITCH` of whole partitions to and from staging tables. Crucially, it also shows a *wrong* merge that forces physical data movement, and benchmarks partitioned vs. non-partitioned access.

## Key techniques
`CREATE PARTITION FUNCTION` (`RANGE RIGHT`), `PARTITION SCHEME`, filegroups/files, `ALTER PARTITION FUNCTION SPLIT/MERGE RANGE`, `ALTER TABLE SWITCH PARTITION`, the `$PARTITION` function, aligned `CHECK` constraints on switch-in tables.

## Trade-offs & when *not* to use it
Partitioning adds substantial administrative complexity — filegroups, aligned indexes, boundary management — and only helps when queries align to the partition key. Metadata-only `SWITCH`/`MERGE` operations require correct boundaries and matching constraints, or they silently trigger expensive data movement. It is an archival/maintenance tool, **not** a general "make my query faster" button.

---
[← 013 Effective-dating & temporal tables](013-history-table-effective-start-end-dates.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 015 Vertical partitioning](015-vertical-partitioning.md)
