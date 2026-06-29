# 023 — Sensitive-Data Protection (Masking, Views, Encryption)

**Script:** [`src/023_sensitive_data_obfuscation.sql`](../../src/023_sensitive_data_obfuscation.sql) · [Pattern index](../../README.md#the-25-patterns)

## The problem
Credit-card data and customer PII must be exposed at **different fidelity to different roles** — full to some, masked to others, denied to the rest — with access auditing, and without ever leaking full values to the unauthorized.

## The solution
Demonstrates four layered approaches so you can pick the right tool for a given threat model:
1. A **role-aware stored procedure** that returns full / masked / denied data and logs every access.
2. **Obfuscating views** — anonymized GUID, dropped id, role-conditional SHA-256-hashed id.
3. **Dynamic Data Masking** with per-column mask functions and `UNMASK` grants.
4. **Column-level symmetric encryption** (AES-256) via a certificate / master key, with a role-gated decryption procedure.

## Key techniques
`USER_NAME()`/`EXECUTE AS`/`REVERT`, access-log auditing, `FORMAT`/`STUFF` masking, `HASHBYTES('SHA2_256', ...)`, Dynamic Data Masking (partial/default/email functions, `GRANT UNMASK`), `CREATE MASTER KEY`/`CERTIFICATE`/`SYMMETRIC KEY`, `EncryptByKey`/`DecryptByKey`, `IS_MEMBER`, `NTILE`/`ROW_NUMBER` in views.

## Trade-offs & when *not* to use it
A crucial distinction: **Dynamic Data Masking is presentation-only and trivially bypassed — it is not a security boundary.** Hashed/GUID views can still be correlated (or are irreversible when you actually needed the value back), and column encryption breaks indexing/seeking on the encrypted column while adding key-management burden. Choose based on the *actual* threat model, not on which feature sounds most secure.

---
[← 022 Computed columns & maintained aggregates](022-computed-column.md) · [Pattern index](../../README.md#the-25-patterns) · [next → 024 Numbers/calendar tables & indexed views](024-precalculated-tables-and-indexed-views.md)
