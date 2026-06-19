# 018 — Subtype Table for Hardware + Reassembly View

**Script:** [`src/018_hardware_product_table.sql`](../../src/018_hardware_product_table.sql) · [Pattern index](../../README.en.md#the-26-patterns)

## The problem
Just like software ([017](017-software-product-table.md)), hardware-only attributes (`weight`, dimensions) sit on the base `product` table as mostly-`NULL` columns. But application code still wants to read a single, unified product shape — it shouldn't have to know about the subtype split.

## The solution
Creates a `hardware_product` subtype table (shared-PK FK), populates it for the non-software products via the category path, and drops the dimension columns from `product`. It then creates a `product_view` that `LEFT JOIN`s the base table to *both* subtype tables to reconstruct the original flat shape — so readers get one tidy view while writes stay normalized.

## Key techniques
Subtype table, shared-PK FK, path-based category exclusion (`NOT LIKE`), `LEFT JOIN` reassembly `VIEW`, `INSERT ... SELECT` migration.

## Trade-offs & when *not* to use it
The reassembly view reintroduces the wide, nullable shape and pays for two outer joins on every read. The supertype/subtype design trades simpler, smaller writes for more complex reads — worthwhile only when the subtypes carry substantial distinct attributes. If reads dominate and the attribute sets are small, the original flat table may simply be the better engineering choice.

---
[← 017 Subtype table (software)](017-software-product-table.md) · [Pattern index](../../README.en.md#the-26-patterns) · [next → 019 Multi-language data & collations](019-handling-multilanguage-data.md)
