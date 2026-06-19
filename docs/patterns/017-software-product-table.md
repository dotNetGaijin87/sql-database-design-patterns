# 017 — Subtype Table for Software Products

**Script:** [`src/017_software_product_table.sql`](../../src/017_software_product_table.sql) · [Pattern index](../../README.en.md#the-26-patterns)

## The problem
The `product` table holds software-only attributes — `compatible_os`, `required_ram`, `required_disk_space` — that are `NULL` for every hardware product. That wastes space and muddies the meaning of the table (a "product" is really two different things).

## The solution
Creates a `software_product` subtype table that shares the product primary key via a foreign key, populates it only for products in the "Software Products" category branch (using the category path/hierarchy), and drops the software-specific columns from the base `product` table. This is the class-table-inheritance / supertype-subtype pattern.

## Key techniques
Class-table inheritance (subtype table sharing the PK as an FK), path-based category filtering with a `LIKE` subquery, `INSERT ... SELECT` migration, `ALTER TABLE DROP COLUMN`.

## Trade-offs & when *not* to use it
Retrieving a complete product now requires joining the right subtype table, and a shared-PK FK doesn't, by itself, stop a product from accidentally having *both* subtype rows. This split only pays off when the attribute sets are genuinely disjoint and substantial — otherwise a few nullable columns are simpler. (See [018](018-hardware-product-table.md) for the reassembly view that hides the split from readers.)

---
[← 016 Hierarchical data](016-modeling-hierarchical-data.md) · [Pattern index](../../README.en.md#the-26-patterns) · [next → 018 Subtype table + reassembly view](018-hardware-product-table.md)
