# Validated findings (running count 0)

- 3 lead(s) marked VALID at 2026-09-05 19:46:27 UTC
  - | Q4 Provable | NO | All endpoints require valid Token auth; no self-service registration confirmed (/_exceptions routes are SPA fallback, user-registration 401, tenant-registration 403); cannot exerc
  - **Verdict: HOLD** — Highest-value hypothesis but gated on human credential provision. Needs valid low-priv account to verify.
  - | Q4 Provable | NO | Requires valid 32-hex token format knowledge; enumeration at scale may trigger WAF; response differentiation unconfirmed |
