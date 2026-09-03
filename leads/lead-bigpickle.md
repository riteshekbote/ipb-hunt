## 2026-09-03 16:32:29 UTC [target] (model bigpickle)
[PRIO] pluto.portal.ipb.de,7.7,multi-tenancy/admin API (auth-gated IDOR/BOLA surface)
[PRIO] event.ipb.de,4.5,pretix ticketing/control
[PRIO] www.ipb.de,3.0,Plesk PHP static
[NEW] pluto.portal.ipb.de live — React SPA backed by DRF multi-tenancy API (200)
[NEW] event.ipb.de live — pretix ticketing (200), nginx/1.31.4
[NEW] www.ipb.de live — PHP/8.3.33 on PleskLin (200)
[NEW] ipb.de → 301 → www.ipb.de
[CHANGED] `.env`/`server-info` on www.ipb.de → 403 (blocked, not exposed)
[HYP] Multi-tenancy IDOR/BOLA across user/membership/tenant endpoints
[HYP] Kiosk token check-in auth bypass / user enumeration
[HYP] Unauthenticated CSRF token issuance via /api/ct/
[PARKED] Kiosk token bypass: token unknown, cannot test without token → confidence <40
[PARKED] CSRF token leak: rotates per request, no session binding → low impact
[FINAL] Multi-tenancy IDOR (needs auth) / check-in enumeration
[NEXT] HUMANAUTH — need a valid account on pluto.portal.ipb.de to test the multi-tenant IDOR/BOLA surface (user/me, membership, tenant-relationship, user-token)
[LEARN] ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
[LEARN] REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
[RISK] ipb: 55 — pluto portal exposes a very large authenticated API; any account grants deep multi-tenant access; gated by auth so unauthenticated risk moderate
[NEW] pluto.portal.ipb.de live — Django REST Framework multi-tenant customer portal (React SPA)
[NEW] event.ipb.de live — pretix ticketing (nginx/1.31.4)
[NEW] www.ipb.de live — PHP/8.3.33 on PleskLin
[NEW] ipb.de → 301 → www.ipb.de
[CHANGED] `/.env`, `/server-info` on www.ipb.de → 403 (blocked, not exposed)
[PRIO] pluto.portal.ipb.de,7.15,BOLA-IDOR (DRF multi-tenancy + admin exports; auth-gated but high business value)
[PRIO] event.ipb.de,4.95,pretix-broken-auth (mature hardening: /control 403, /redirect allowlisted)
[PRIO] www.ipb.de,3.00,Plesk-static
[HYP] Cross-tenant BOLA across membership/tenant/user endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/... and /api/admin/multi-tenancy/v1/...
confidence: 60
reasoning: DRF multi-tenancy exposes per-tenant user/membership/relationship objects with EAV + bulk export endpoints; object IDs are sequential; per-tenant authorization is the prime BOLA chokepoint. All endpoints return `Authentication credentials were not provided` unauthenticated.
evidence_needed: authenticated low-priv account reading another tenant's user/membership/relationship objects by ID
verify_steps: POST /api/session/ {username,password} to obtain session; GET /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/ across tenant boundaries as low-priv user
impact: cross-tenant PII dump / privilege escalation — HIGH
testability: AUTH_HELPED
[HYP] Admin dynamic-config mass assignment override
class: MISCONFIG
asset: pluto.portal.ipb.de /api/admin/dynamic-config/v1/config-namespace/apply/
confidence: 45
reasoning: SPA calls adminCreateDynamicConfig with arbitrary keys → apply/; no scoping in bundle; config namespaces (branding/motd/dns) could be overridden.
evidence_needed: authenticated admin (or elevated) writing to another namespace
impact: config tampering / defacement
testability: AUTH_HELPED
[HYP] Check-in kiosk token user-enumeration
class: AUTH
asset: pluto.portal.ipb.de /api/check-in/v1/check-in/kiosk_login/
confidence: 45
reasoning: POST {token} returns `No User matches the given query.` vs other responses for invalid — token existence oracle; no rate limit observed.
evidence_needed: enumerate valid 32-hex kiosk tokens without auth
impact: low alone, weakens kiosk gate
testability: AUTH_HELPED
[PARKED] CSRF token leak via /api/ct/: token rotates per request, not bound to any session — no replay/CSRF value. <40.
[PARKED] open-redirect on event.ipb.de /redirect/: allowlisted (400 on arbitrary URLs); rejected class anyway.
[PARKED] www.ipb.de .env/server-info: 403, not exposed.
[FINAL] Cross-tenant BOLA (60) > dynamic-config mass-assignment (45) > kiosk enumeration (45)
[NEXT] HUMANAUTH: sponsor a low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/
[LEARN] ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/.
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here.
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked).
[RISK] ipb: 55 — pluto.portal.ipb.de exposes an unusually large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI); surface is high but gated by auth. Unauthenticated risk moderate. Highest exposure emerges once any credentialed account is obtained.
## 2026-09-03 19:25:33 UTC [target] (model bigpickle)
[PRIO] pluto.portal.ipb.de,7.15,BOLA-IDOR (DRF multi-tenancy; auth-gated but highest business value — PII/tenant access)
[PRIO] event.ipb.de,4.90,pretix-broken-auth (mature hardening confirmed: /control 403, /redirect allowlisted)
[PRIO] www.ipb.de,3.00,Plesk-static (env/config 403 blocked)
[HYP] Cross-tenant BOLA across membership/tenant/user endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/ and /user/{id}/
confidence: 60
reasoning: DRF multi-tenancy exposes per-tenant user/membership/relationship objects with EAV + bulk export; sequential object IDs; per-tenant authorization is the prime BOLA chokepoint; all unauthenticated calls return `Authentication credentials were not provided`.
evidence_needed: authenticated low-priv account reading another tenant's user/membership/relationship objects by ID
verify_steps: POST /api/session/ {username,password} then GET /api/multi-tenancy/v1/tenant/{id}/ and /user/{id}/ across tenant boundaries as low-priv user
impact: cross-tenant PII dump / privilege escalation — HIGH
testability: AUTH_HELPED
[HYP] Check-in kiosk token user-enumeration oracle
class: AUTH
asset: pluto.portal.ipb.de /api/check-in/v1/check-in/kiosk_login/
confidence: 45
reasoning: POST {token} returns distinct `No User matches the given query.` response; token existence oracle; 32-hex token space; no rate-limit observed in bundle.
evidence_needed: enumerate valid 32-hex kiosk tokens unauthenticated
verify_steps: POST /api/check-in/v1/check-in/kiosk_login/ with header `X-CSRFToken` + `Cookie: csrftoken` for none vs varying tokens; compare status/body
impact: weakens kiosk gate; low alone
testability: AUTH_HELPED
[HYP] Admin dynamic-config namespace mass-assignment
class: MISCONFIG
asset: pluto.portal.ipb.de /api/admin/dynamic-config/v1/config-namespace/apply/
confidence: 45
reasoning: SPA calls adminCreateDynamicConfig with arbitrary keys → apply/; no scoping in bundle; config namespaces (branding/motd/dns) could be overridden if an admin-level session is obtained.
evidence_needed: authenticated admin writing to a namespace it should not scope
verify_steps: with admin session, POST apply/ supplying keys outside expected namespace; check for 200 and reflected config override
impact: config tampering / defacement
testability: AUTH_HELPED
[PARKED] CSRF token leak via /api/ct/: rotates per request, no session binding — no replay/CSRF value. <40.
[PARKED] open-redirect event.ipb.de /redirect/: allowlisted (400 on arbitrary URLs); REJECTED class.
[PARKED] www.ipb.de .env/server-info: 403, not exposed; REJECTED class.
[FINAL] BOLA-IDOR (60) > kiosk token enum (45) > admin dynamic-config (45)
[NEXT] HUMANAUTH: sponsor a low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/ — the single highest-value unverified hypothesis, gated solely on having any valid account.
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending credentialed access.
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle.
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
[RISK] ipb: 55 — pluto.portal exposes a very large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI). Surface high but gated by auth. Unauthenticated risk moderate; highest exposure materializes once any credentialed account is obtained, which only a human sponsor can provide.
## 2026-09-03 21:52:59 UTC [target] (model bigpickle)
[PRIO] pluto.portal.ipb.de,7.15,BOLA-IDOR (DRF multi-tenancy; auth-gated but highest business value — PII/tenant access)
[PRIO] event.ipb.de,4.90,pretix-broken-auth (mature hardening confirmed: /control 403, /redirect allowlisted)
[PRIO] www.ipb.de,3.00,Plesk-static (env/config 403 blocked)
[HYP] Cross-tenant BOLA across membership/tenant/user endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/ and /user/{id}/
confidence: 60
reasoning: DRF multi-tenancy exposes per-tenant user/membership/relationship objects with EAV + bulk export; sequential object IDs; per-tenant authorization is the prime BOLA chokepoint; all unauthenticated calls return `Authentication credentials were not provided`.
evidence_needed: authenticated low-priv account reading another tenant's user/membership/relationship objects by ID
verify_steps: POST /api/session/ {username,password} then GET /api/multi-tenancy/v1/tenant/{id}/ and /user/{id}/ across tenant boundaries as low-priv user
impact: cross-tenant PII dump / privilege escalation — HIGH
testability: AUTH_HELPED
[HYP] Check-in kiosk token user-enumeration oracle
class: AUTH
asset: pluto.portal.ipb.de /api/check-in/v1/check-in/kiosk_login/
confidence: 45
reasoning: POST {token} returns distinct `No User matches the given query.` response; token existence oracle; 32-hex token space; no rate-limit observed in bundle.
evidence_needed: enumerate valid 32-hex kiosk tokens unauthenticated
verify_steps: POST /api/check-in/v1/check-in/kiosk_login/ with header `X-CSRFToken` + `Cookie: csrftoken` for none vs varying tokens; compare status/body
impact: weakens kiosk gate; low alone
testability: AUTH_HELPED
[HYP] Admin dynamic-config namespace mass-assignment
class: MISCONFIG
asset: pluto.portal.ipb.de /api/admin/dynamic-config/v1/config-namespace/apply/
confidence: 45
reasoning: SPA calls adminCreateDynamicConfig with arbitrary keys → apply/; no scoping in bundle; config namespaces (branding/motd/dns) could be overridden if an admin-level session is obtained.
evidence_needed: authenticated admin writing to a namespace it should not scope
verify_steps: with admin session, POST apply/ supplying keys outside expected namespace; check for 200 and reflected config override
impact: config tampering / defacement
testability: AUTH_HELPED
[PARKED] CSRF token leak via /api/ct/: rotates per request, no session binding — no replay/CSRF value. <40.
[PARKED] open-redirect event.ipb.de /redirect/: allowlisted (400 on arbitrary URLs); REJECTED class.
[PARKED] www.ipb.de .env/server-info: 403, not exposed; REJECTED class.
[FINAL] BOLA-IDOR (60) > kiosk token enum (45) > admin dynamic-config (45)
[NEXT] HUMANAUTH: sponsor a low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/ — the single highest-value unverified hypothesis, gated solely on having any valid account.
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending credentialed access.
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle.
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
[RISK] ipb: 55 — pluto.portal exposes a very large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI). Surface high but gated by auth. Unauthenticated risk moderate; highest exposure materializes once any credentialed account is obtained, which only a human sponsor can provide.
class: MISCONFIG
asset: pluto.portal.ipb.de /api/admin/dynamic-config/v1/config-namespace/apply/
confidence: 45
reasoning: SPA calls adminCreateDynamicConfig with arbitrary keys → apply/; no scoping in bundle; config namespaces (branding/motd/dns) could be overridden by elevated/admin accounts
evidence_needed: authenticated admin (or elevated) writing to another namespace
verify_steps: POST /api/session/ {admin_creds} to obtain session; POST /api/admin/dynamic-config/v1/config-namespace/apply/ with cross-namespace payload
impact: config tampering / defacement / DNS hijack via branding/motd/dns namespaces — MEDIUM-HIGH
testability: AUTH_HELPED
[HYP] Check-in kiosk token user-enumeration via /api/check-in/v1/check-in/kiosk_login/
class: AUTH
asset: pluto.portal.ipb.de /api/check-in/v1/check-in/kiosk_login/
confidence: 45
reasoning: POST {token} returns `No User matches the given query.` vs other responses for invalid tokens — token existence oracle; no rate limit observed on endpoint
evidence_needed: enumerate valid 32-hex kiosk tokens without auth
verify_steps: POST /api/check-in/v1/check-in/kiosk_login/ with candidate 32-hex tokens; observe response differentiation
impact: low alone, weakens kiosk gate; enables targeted token brute-force — LOW-MEDIUM
testability: PASSIVE
[PARKED] Admin dynamic-config mass assignment override: confidence 45 but requires admin/elevated creds (HUMAN_ONLY gate), no path to obtain creds
[PARKED] Check-in kiosk token user-enumeration: confidence 45 but endpoint requires valid token format knowledge; enumeration at scale may trigger WAF
[FINAL] Cross-tenant BOLA across membership/tenant/user endpoints (confidence 60) — highest confidence, clear verify path, HIGH impact
[NEXT] HUMANAUTH: sponsor/obtain a valid low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/
[LEARN] REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
[LEARN] REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
[LEARN] ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
[LEARN] REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
[LEARN] ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] ACCEPTED cross-tenant BOLA @ pluto.portal.ipb.de: DRF multi-tenancy API with sequential IDs, auth-gated but per-tenant auth is sole BOLA control
[RISK] ipb: 55 — pluto.portal.ipb.de exposes an unusually large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI); surface is high but gated by auth. Unauthenticated risk moderate. Highest exposure emerges once any credentialed account is obtained. Wildcard DNS on *.ipb.de still masks potential additional attack surface (app, auth, cloud, my, prod subdomains unresolved).
[HYP] Cross-tenant BOLA across membership/tenant/user endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/ and /user/{id}/
confidence: 60
reasoning: DRF multi-tenancy exposes per-tenant user/membership/relationship objects (EAV + bulk export) with sequential object IDs; per-tenant authorization is the prime BOLA chokepoint; all unauthenticated calls return `Authentication credentials were not provided`. No new unauthenticated vector found this cycle; full confirm still gated on credentials.
evidence_needed: authenticated low-priv account reading another tenant's user/membership/relationship objects by ID
verify_steps: POST /api/session/ {username,password} then GET /api/multi-tenancy/v1/tenant/{id}/ and /user/{id}/ across tenant boundaries as low-priv user
impact: cross-tenant PII dump / privilege escalation — HIGH
testability: AUTH_HELPED
[PARKED] kiosk_login token oracle (45): requires valid 32-hex token format + repeated POSTs; WAF/rate-limit risk, low alone.
[PARKED] admin dynamic-config mass-assignment (45): needs elevated creds, no path to obtain — gated on same human sponsor as BOLA.
[FINAL] Cross-tenant BOLA (60) > dynamic-config mass-assignment (45) > kiosk enumeration (45).
[NEXT] HUMAN: sponsor a low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/ — sole unverified high-value hypothesis, gated entirely on having any valid account.
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending credentialed access.
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle.
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
[RISK] ipb: 55 — pluto.portal exposes a very large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI). Surface high but gated by auth. Unauthenticated risk moderate; highest exposure materializes once any credentialed account is obtained, which only a human sponsor can provide. Wildcard DNS still masks additional (app/auth/cloud/my/prod) subdomains.
## 2026-09-03 23:42:42 UTC [target] (model bigpickle)
[NEW] App name identified: pluto.portal.ipb.de SPA = "EdgePortal" (title in index.html)
[NEW] Current bundle surfaced additional endpoints absent from prior leads: /api/system/ (GET/HEAD/OPTIONS, 401 Token-auth), /api/check-in/v1/check-in/kiosk_totp/validate/ (405 POST-only), /api/multi-tenancy/v1/association-request/, /api/admin/dns/v1/system-token/regenerate/, /api/inventory/v1/tenant/self/ — ALL confirmed auth-gated (401 application/json `Authentication credentials were not provided`, WWW-Authenticate: Token)
[CHANGED] Deep bundle probe this cycle: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/docs, /api/openapi all return SPA fallback (200 text/html, 354606 bytes) — NO DRF schema/OpenAPI exposure; no unauthenticated config leak
[CHANGED] /api/system/ confirmed real live DRF endpoint returns 401 not SPA fallback; API surface remains uniformly auth-gated
[PRIO] pluto.portal.ipb.de,7.20,BOLA-IDOR (EdgePortal DRF multi-tenancy; auth-gated but PII/tenant value highest; no unauth vector found anew)
[PRIO] event.ipb.de,4.90,pretix-broken-auth (mature hardening: /control 403, /redirect allowlisted)
[PRIO] www.ipb.de,3.00,Plesk-static (env/config 403 blocked)
[HYP] Cross-tenant BOLA across tenant/user/membership/association-request endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /user/{id}/, /association-request/, /user/eav/
confidence: 60
reasoning: EdgePortal DRF multi-tenancy exposes per-tenant user/membership/relationship/EAV + bulk export with sequential object IDs; per-tenant authorization is the sole BOLA chokepoint (401 when unauthenticated, WWW-Authenticate: Token). New surface this cycle: association-request, user-token, tenant/self, inventory/tenant/self — all IDs sequential and auth-gated. No new unauth vector found after drilling schema/config/sites paths (all SPA fallback or 401).
evidence_needed: authenticated low-priv account reading another tenant's user/membership/relationship/association-request objects by ID
verify_steps: POST /api/session/ {username,password} to obtain Token; GET /api/multi-tenancy/v1/tenant/{id}/, /user/{id}/, /association-request/{id}/, /tenant/self/ across tenant boundaries as low-priv user
impact: cross-tenant PII dump / privilege escalation — HIGH
testability: AUTH_HELPED
[HYP] Check-in kiosk TOTP/user enumeration via kiosk_token/kiosk_totp validate
class: AUTH
asset: pluto.portal.ipb.de /api/admin/check-in/v1/check-in/kiosk_token/ and /api/check-in/v1/check-in/kiosk_totp/validate/
confidence: 45
reasoning: Bundle contains admin kiosk_token provisioning + kiosk_totp validate endpoints (POST-only 405); token/TOTP existence oracle may differentiate responses; 32-hex token space.
evidence_needed: response differentiation for valid vs invalid kiosk token/TOTP without auth
verify_steps: OPTIONS/GET on kiosk_token and kiosk_totp/validate to map required fields; POST minimal candidate payloads comparing status/body
impact: weakens kiosk gate / targeted brute — LOW-MEDIUM
testability: AUTH_HELPED
[PARKED] Admin dynamic-config mass-assignment /api/admin/dynamic-config/v1/config-namespace/apply/: needs elevated creds, no path to obtain — same HUMAN gate as BOLA.
[PARKED] kiosk_token/kiosk_totp enumeration: POST-only, needs token-format knowledge, WAF/rate-limit risk, low alone.
[PARKED] kiosk_login token oracle: unchanged, low alone, WAF risk.
[FINAL] Cross-tenant BOLA incl. association-request/user-token/user-eav surfaces (60) — highest confidence; all other avenues exhaustively parked or rejected; entire surface uniformly auth-gated.
[NEXT] HUMAN: sponsor a low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/, /user/{id}/, /association-request/{id}/ — sole unverified high-value hypothesis; every alternative (schema/config/session/check-in) confirmed auth-gated or SPA-fallback this cycle.
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback).
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: EdgePortal multi-tenancy is prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access.
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /schema, /swagger, /openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface.
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
[RISK] ipb: 55 — EdgePortal pluto.portal.ipb.de exposes a very large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI, association-request). Surface high (expanded this cycle) but uniformly gated by Token auth. Unauth risk moderate (only /api/ct/ CSRF token + login/405 endpoints exposed). Highest exposure materializes once any credentialed account is obtained — strictly HUMAN-gated.
class: IDOR / asset: pluto.portal.ipb.de `/api/multi-tenancy/v1/tenant/{id}/`, `/user/{id}/`, `/association-request/{id}/` / confidence: 60 / reasoning: seq-ID per-tenant objects, Token-auth sole chokepoint, all 401 unauth; expanded surface this cycle / verify: POST `/api/session/` → GET tenant/user/association-request IDs cross-tenant / impact: HIGH / testability: AUTH_HELPED
class: AUTH / asset: `/api/check-in/v1/check-in/kiosk_totp/validate/` / confidence: 45 / testability: AUTH_HELPED
