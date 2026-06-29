# 021 — Denormalization with JSON Documents

**Script:** [`src/021_denormalization_with_json.sql`](../../src/021_denormalization_with_json.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
Assembling a product's reviews means joining `review` to `review_image` and aggregating image URLs on *every* read. For a read-heavy feature like a product page, that join cost adds up.

## The solution
Creates a `product_review` table whose `content` column stores a **JSON document** (author, rating, text, date, and an image array), validated by an `ISJSON` `CHECK` constraint. It migrates the normalized review/review_image data into the document form via `FOR JSON`, drops the old tables, and reads the data back with `OPENJSON`. This is deliberate, controlled denormalization — the inverse direction from the earlier normalization scripts.

## Key techniques
JSON stored in a `VARCHAR` column, `ISJSON` `CHECK` constraint, `FOR JSON PATH` with `WITHOUT_ARRAY_WRAPPER`, `JSON_QUERY`, `STRING_AGG` to build arrays, `OPENJSON WITH (...)` schema, `ROW_NUMBER()` surrogate.

## Trade-offs & when *not* to use it
Denormalizing to JSON speeds the read but loses relational integrity for the nested data (images are no longer FK-enforced rows), makes ad-hoc querying and indexing of inner fields harder, and reintroduces update anomalies. Only justified for read-mostly, naturally document-shaped aggregates — *not* as a default storage strategy.

---
[← 020 Soft delete](020-soft-delete.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 022 Computed columns & maintained aggregates](022-computed-column.md)
