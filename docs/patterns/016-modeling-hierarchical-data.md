# 016 — Hierarchical Data: Path Enumeration vs. HierarchyID

**Script:** [`src/016_modeling_hierarchical_data.sql`](../../src/016_modeling_hierarchical_data.sql) · [Pattern index](../../README.en.md#the-26-patterns)

## The problem
The `product_category` tree is stored as an **adjacency list** (`parent_category_id`). That's simple, but it makes "give me all descendants of this category" or "move this whole subtree" awkward and recursive.

## The solution
Migrates the adjacency list into two alternative models — a **materialized path** string and a native **`hierarchyid`** column (both built with recursive CTEs) — and compares them for ancestor/descendant retrieval and subtree relocation. It also contrasts depth-first vs. breadth-first indexing strategies for `hierarchyid`.

## Key techniques
Recursive CTE, the `hierarchyid` type and its methods (`IsDescendantOf`, `GetAncestor`, `GetDescendant`, `GetLevel`, `GetRoot`, `GetReparentedValue`, `ToString`, `Parse`), materialized path with `LIKE`, a computed `level` column, depth-first vs. breadth-first indexes.

## Trade-offs & when *not* to use it
`hierarchyid` is compact and supports reparenting cleanly, but it's opaque binary that's awkward to read and debug. Path enumeration is human-readable but bloats with depth and is fragile under string manipulation. Both need careful index choices, and neither enforces tree integrity the way a plain adjacency-list FK does — so the "best" model genuinely depends on your read/write mix.

---
[← 015 Vertical partitioning](015-vertical-partitioning.md) · [Pattern index](../../README.en.md#the-26-patterns) · [next → 017 Subtype table (software)](017-software-product-table.md)
