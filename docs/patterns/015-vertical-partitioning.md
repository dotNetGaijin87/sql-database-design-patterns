# 015 — Vertical Partitioning (Column Splitting)

**Script:** [`src/015_vertical_partitioning.sql`](../../src/015_vertical_partitioning.sql) · [Pattern index](../../README.en.md#the-26-patterns)

## The problem
A wide, rarely-read `description` column bloats every `customer` row. Because fewer rows fit per data page, *every* common query that never even touches `description` reads more pages and runs slower.

## The solution
Moves `description` into a separate `customer_description` table (1:1 via a shared primary key / foreign key) so the hot `customer` table has narrow rows, and rebuilds the indexes. The script benchmarks the full vs. split design for single-row, narrow-range and wide-range access — and, as a bonus, shows how clustered-key width (`INT` vs. `NEWID` vs. `NEWSEQUENTIALID`) affects index size and fragmentation.

## Key techniques
Vertical partitioning (1:1 table split), shared-key FK, `ALTER INDEX REBUILD`, page-density inspection, `NEWID` vs. `NEWSEQUENTIALID` vs. `INT` clustered-key fragmentation comparison.

## Trade-offs & when *not* to use it
Splitting columns only helps when the moved columns are *genuinely cold*. If queries frequently need both halves, the extra join can cost more than the I/O you saved — so this backfires for wide-range or combined-column access. Measure your actual access pattern before splitting.

---
[← 014 Horizontal partitioning](014-horizontal-partitioning.md) · [Pattern index](../../README.en.md#the-26-patterns) · [next → 016 Hierarchical data](016-modeling-hierarchical-data.md)
