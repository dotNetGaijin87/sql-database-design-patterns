# 011 — Master-Detail & Filtered Indexes

**Script:** [`src/011_master_detail.sql`](../../src/011_master_detail.sql) · [Pattern index](../../README.en.md#the-26-patterns)

## The problem
A shipment (the *master*) can contain many shipped order items (the *detail*), and the application very frequently needs to find the small set of **incomplete** shipments efficiently — without scanning every shipment ever made.

## The solution
Creates a `sales.order_item_shipment` detail table linked to `order_shipment` and `order_item` via composite foreign keys, adds an `is_complete` BIT flag (with a default) to the master, and then compares a full nonclustered index against a **filtered index** (`WHERE is_complete = 0`) for the "find open shipments" query.

## Key techniques
Master-detail modeling, composite `FOREIGN KEY`s, `CHECK` and `DEFAULT` constraints, a `BIT` flag, a filtered nonclustered index, `SET STATISTICS IO` comparison, index page-size inspection.

## Trade-offs & when *not* to use it
A filtered index only helps queries whose predicate matches the filter, and it still needs maintenance as rows flip the flag. It's wasteful if most queries don't use the filter, or if the filtered subset isn't actually selective (e.g. if *most* shipments are incomplete).

---
[← 010 Many-to-many](010-associative-table.md) · [Pattern index](../../README.en.md#the-26-patterns) · [next → 012 Status history & event sourcing](012-history-table-effective-date.md)
