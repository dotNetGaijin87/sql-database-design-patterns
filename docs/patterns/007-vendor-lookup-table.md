# 007 — Lookup Table (Eliminating Repeated Strings)

**Script:** [`src/007_vendor_lookup_table.sql`](../../src/007_vendor_lookup_table.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
`vendor_name` is stored as a repeated free-text string on every single product. That duplicates data, wastes space, invites typos ("Acme" vs. "ACME Inc."), and gives you no way to enforce a canonical list of vendors.

## The solution
Creates a `production.vendor` table seeded with the distinct vendor names, adds a `vendor_id` foreign key to `product`, backfills it via a join, enforces it with an FK, indexes it, and drops the now-redundant `vendor_name` column.

## Key techniques
`SELECT DISTINCT` seeding, surrogate `IDENTITY` key, `FOREIGN KEY WITH CHECK` and `ON UPDATE CASCADE`, `UPDATE ... FROM` join, nonclustered index on the FK.

## Trade-offs & when *not* to use it
Displaying a product's vendor now requires a join, and you have one more table to maintain. For a *tiny, static* set of values, a simple `CHECK` constraint or an application enum can be simpler than a full lookup table — the lookup table earns its keep when the set is non-trivial, shared, or carries its own attributes.

---
[← 006 Data cleansing & format enforcement](006-simple-format.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 008 Reference table](008-state-lookup-table.md)
