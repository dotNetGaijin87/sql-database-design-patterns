# 019 — Multi-Language Data, Collations & Translation Tables

**Script:** [`src/019_handling_multilanguage_data.sql`](../../src/019_handling_multilanguage_data.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
The app must store, sort and compare data in multiple languages (English, Polish, Japanese). Get collation or `VARCHAR` vs. `NVARCHAR` wrong and you silently get wrong sort orders, lost accents, and — subtly — full index scans from implicit conversions.

## The solution
Demonstrates collation inspection, case-sensitivity effects, and the implicit-conversion performance trap (`VARCHAR` column vs. an `N''` literal under a SQL collation). It implements per-language sorting via `ROW_NUMBER()` with a column-level `COLLATE`, builds a proper translation model (a `language` table plus a `product_category_translation` table with an `is_default` flag), and migrates the customer name columns from `VARCHAR` to `NVARCHAR` in batches to preserve accents.

## Key techniques
Collations (SQL vs. Windows, CI/CS, binary), the `COLLATE` clause, `fn_helpcollations`, implicit-conversion/sargability analysis, `ROW_NUMBER() OVER` with per-collation sort, translation lookup tables with an `is_default` flag, batched `VARCHAR`→`NVARCHAR` migration with `sp_rename`.

## Trade-offs & when *not* to use it
`NVARCHAR` doubles storage for text that never actually needs Unicode, and a translation table adds a join to every localized read. Mixing `VARCHAR` columns with `N''` literals under SQL collations forces index scans. The lesson: collation and type choices must be **deliberate**, not whatever the column defaulted to.

---
[← 018 Subtype table + reassembly view](018-hardware-product-table.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 020 Soft delete](020-soft-delete.md)
