# 010 — Many-to-Many (Junction Table)

**Script:** [`src/010_associative_table.sql`](../../src/010_associative_table.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
A customer's wishlist is stored as a **comma-separated string of product names** in a single column. It's unqueryable ("which customers want product X?"), unvalidated (the names can drift from real products), and has zero referential integrity.

## The solution
Creates a `sales.customer_wishlist` associative (junction) table with a composite primary key `(customer_id, product_id)` and foreign keys to both parents, then populates it by splitting the CSV string and joining to the product table — finally dropping the offending `wishlist` column. This is the canonical many-to-many model.

## Key techniques
Associative/junction table, composite `PRIMARY KEY`, dual `FOREIGN KEY`s with cascade, `STRING_SPLIT`, `CROSS APPLY`, `INSERT ... SELECT`.

## Trade-offs & when *not* to use it
The composite PK allows each product at most once per customer. A junction table is the *correct* model for many-to-many, but it does add join overhead compared to the (broken) single-column approach — the point is that the single-column approach was never actually saving you anything once you needed to query it.

---
[← 009 Enum → lookup table](009-order-status-type-lookup-table.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 011 Master-detail & filtered indexes](011-master-detail.md)
