# 004 — Extracting Sensitive Columns (1-to-Many)

**Script:** [`src/004_splitting_customer_table.sql`](../../src/004_splitting_customer_table.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
Credit-card fields are crammed directly into the `customer` row. That limits each customer to exactly one card, and — worse — mixes sensitive payment data in with everyday profile data, so anything that reads a customer also reads their card.

## The solution
Creates a `sales.credit_card` table keyed by the card id with a foreign key back to customer, migrates the card columns out of `customer`, and then drops them from the customer table. A customer can now have many cards, and the payment data is isolated.

## Key techniques
Table extraction, `FOREIGN KEY` with `ON DELETE/UPDATE CASCADE`, inline index definition, `INSERT ... SELECT` migration, `ALTER TABLE DROP COLUMN`.

## Trade-offs & when *not* to use it
Reading a customer together with their card now requires a join. The split is only worth it if you genuinely need multiple cards per customer, or want to isolate sensitive data for security reasons — otherwise it just adds query complexity for no benefit.

---
[← 003 Indexing foreign keys](003-foreign-key.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 005 Zero-downtime column split](005-splitting-name-column.md)
