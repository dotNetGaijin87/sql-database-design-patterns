# 006 — Data Cleansing & Format Enforcement

**Script:** [`src/006_simple_format.sql`](../../src/006_simple_format.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
The `phone` column holds inconsistently formatted free text — spaces, parentheses, dashes, even letters — which makes it useless for validation, matching or de-duplication.

## The solution
Strips every non-numeric character from existing values using `TRANSLATE`/`REPLACE`, then adds a `CHECK` constraint that forces future values to be exactly 10 digits with no non-numeric characters. Bad data is both cleaned *and* prevented at the door.

## Key techniques
`TRANSLATE`, `REPLACE`, `REPLICATE`, `TRIM`, `CROSS APPLY (VALUES ...)`, a `CHECK` constraint using a `LIKE` pattern (`'%[^0-9]%'`).

## Trade-offs & when *not* to use it
A rigid "10 digits only" rule cannot represent international numbers or extensions. Format enforcement is powerful but must match your actual domain — if the app needs to support non-US phone formats, this constraint is too strict and should be relaxed or moved into application-level validation.

---
[← 005 Zero-downtime column split](005-splitting-name-column.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 007 Lookup table](007-vendor-lookup-table.md)
