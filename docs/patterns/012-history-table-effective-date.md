# 012 — Status History & Event Sourcing

**Script:** [`src/012_history_table_effective_date.sql`](../../src/012_history_table_effective_date.sql) · [Pattern index](../../README.en.md#the-26-patterns)

## The problem
The `order` table only holds the *current* status, so there's no record of **when** an order moved between states. You can't answer "how long did it take from payment to shipment?" if you only ever overwrite the status in place.

## The solution
Creates an `order_status_history` table and an `AFTER INSERT/UPDATE` trigger that appends a timestamped row on every order change, enabling temporal analytics. A second part demonstrates the **event-sourcing** pattern: an append-only JSON event log that a stored procedure replays to derive a customer's current point balance.

## Key techniques
`AFTER` trigger, append-only history table, `JSON_VALUE` event parsing, `CHECK`-constrained `event_type`, cursor-based replay, `DATEDIFF` analytics, `NVARCHAR(MAX)` event payloads.

## Trade-offs & when *not* to use it
History and event tables grow without bound and need a retention strategy. Rebuilding current state by replaying every event (event sourcing) is slow without periodic snapshots — and the cursor-based replay shown here is especially poor at high volume. Reach for event sourcing when the *audit trail itself* is a first-class business requirement, not just to avoid an `UPDATE`.

---
[← 011 Master-detail & filtered indexes](011-master-detail.md) · [Pattern index](../../README.en.md#the-26-patterns) · [next → 013 Effective-dating & temporal tables](013-history-table-effective-start-end-dates.md)
