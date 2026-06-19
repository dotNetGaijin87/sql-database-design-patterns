# 008 — Reference Table (Validation)

**Script:** [`src/008_state_lookup_table.sql`](../../src/008_state_lookup_table.sql) · [Pattern index](../../README.md#the-26-patterns)

## The problem
The customer `state` column is a free-text 2-character field. Nothing guarantees it actually holds a valid US state code — "XX", "ZZ" or a typo all sail straight in.

## The solution
Creates a `person.state` reference table keyed by the 2-letter code and seeded with all US states and territories, then adds a foreign key from `customer.state` plus a supporting index. Invalid state codes now become impossible to insert.

## Key techniques
Reference table with a natural `CHAR/VARCHAR(2)` primary key, static `VALUES` seeding, `FOREIGN KEY WITH CHECK` and `ON UPDATE CASCADE`, nonclustered index on the FK.

## Trade-offs & when *not* to use it
The FK adds a validation lookup on every customer insert/update. For an essentially fixed list, a `CHECK` constraint with an inline value list is lighter weight — but it's harder to query against and to extend. Use a reference table when you also want to *join* to the list (e.g. to show the full state name) or attach extra columns to it.

---
[← 007 Lookup table](007-vendor-lookup-table.md) · [Pattern index](../../README.md#the-26-patterns) · [next → 009 Enum → lookup table](009-order-status-type-lookup-table.md)
