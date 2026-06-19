# 013 — Effective-Dating & System-Versioned Temporal Tables

**Script:** [`src/013_history_table_effective_start_end_dates.sql`](../../src/013_history_table_effective_start_end_dates.sql) · [Pattern index](../../README.en.md#the-26-patterns)

## The problem
Some data is only valid for a window of time — a promotion runs from one date to another — and the business needs **both** the validity range *and* a full audit trail of how the data changed over time. These are two different questions, and conflating them leads to bugs.

## The solution
Part 1 builds a `promotion` table with explicit `effective_start_date` / `effective_end_date` columns and ranks revenue per promotion period (including the neat trick of deriving each period's end from the *next* row's start). Part 2 rebuilds it as a **system-versioned temporal table** that automatically maintains a history table, then demonstrates point-in-time querying: `AS OF`, `ALL`, `BETWEEN`, and even recovering a deleted row.

## Key techniques
Effective-date range modeling, `RANK() OVER (PARTITION BY ...)`, correlated subquery for the next period's start, `SYSTEM_VERSIONING` temporal tables, `PERIOD FOR SYSTEM_TIME`, `GENERATED ALWAYS AS ROW START/END`, `HISTORY_RETENTION_PERIOD`, `FOR SYSTEM_TIME AS OF/ALL/BETWEEN`, `LAG()`.

## Trade-offs & when *not* to use it
This is the key distinction the script teaches: system-versioned temporal tables capture **transaction time** (when a row physically changed), *not* **business validity time** (when a promotion is meant to apply). Use temporal tables for audit/"what did the row look like then"; use explicit effective-date columns for business validity. Temporal history also grows with every update, and manual date ranges require application discipline to avoid gaps and overlaps.

---
[← 012 Status history & event sourcing](012-history-table-effective-date.md) · [Pattern index](../../README.en.md#the-26-patterns) · [next → 014 Horizontal partitioning](014-horizontal-partitioning.md)
