# Validated findings (running count 0)

- 3 lead(s) marked VALID at 2026-09-05 19:46:27 UTC
  - | Q4 Provable | NO | All endpoints require valid Token auth; no self-service registration confirmed (/_exceptions routes are SPA fallback, user-registration 401, tenant-registration 403); cannot exerc
  - **Verdict: HOLD** — Highest-value hypothesis but gated on human credential provision. Needs valid low-priv account to verify.
  - | Q4 Provable | NO | Requires valid 32-hex token format knowledge; enumeration at scale may trigger WAF; response differentiation unconfirmed |

- 3 lead(s) marked VALID at 2026-09-13 20:59:03 UTC
  - | **Q4 Provable** | **NO** — requires two tenant accounts with valid Tokens; not provable without credentials |
  - | **Q4 Provable** | **NO** — requires valid NC session to enumerate ExApps |
  - **VALID leads: 0**

- 2 lead(s) marked VALID at 2026-09-14 23:40:32 UTC
  - | Q3 Impact | **LOW** — enumeration of valid kiosk tokens alone; weakens kiosk gate but no direct data exposure |
  - **Verdict: HOLD** — Needs confirmation: (1) is RackTables actually deployed/accessible? (2) is `remote.php` actively used for device management? Cannot determine from code review alone. If deployed + 

- 6 lead(s) marked VALID at 2026-09-16 17:09:29 UTC
  - **Verdict: VALID**
  - **Verdict: VALID**
  - | Q2 Reachable? | YES — but requires valid NC session |
  - | Q4 Provable? | NO — unauth returns 404; needs valid NC session |
  - | 1 | Cross-tenant BOLA (EdgePortal) | **VALID** | 8.8 | Report via bugs.olivermaicher.eu |
  - | 2 | Avatar upload stored-XSS (EdgePortal) | **VALID** | 8.1 | Report via bugs.olivermaicher.eu |
