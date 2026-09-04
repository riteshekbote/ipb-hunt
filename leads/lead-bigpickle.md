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
## 2026-09-04 01:58:04 UTC [target] (model bigpickle)
[PRIO] pluto.portal.ipb.de,7.20,BOLA-IDOR (EdgePortal DRF multi-tenancy; auth-gated but PII/tenant value highest; no unauth vector found anew)
[PRIO] event.ipb.de,4.90,pretix-broken-auth (mature hardening: /control 403, /redirect allowlisted; saturated — reject re-probe)
[PRIO] www.ipb.de,3.00,Plesk-static (env/config 403 blocked; saturated)
[PRIO] eticket.ipb.de,4.50,potentially-live-ticketing (never individually re-confirmed; "eticket" aligns with event.ipb.de pretix ecosystem; could expose ticketing API or admin)
[PRIO] nc.ipb.de,4.00,potentially-live-nextcloud (never individually re-confirmed; "nc" = Nextcloud; self-hosted cloud = auth + file upload + WebDAV surface)
[PRIO] gold.ipb.de,3.50,potentially-live-gold-tier (never individually re-confirmed; gold.* subdomain family has 4 variants in inventory)
[PRIO] piwik.ipb.de,3.00,potentially-live-analytics (Matomo/Piwik; analytics panels can expose PII/traffic data if default-auth)
[PRIO] survey.ipb.de,2.50,potentially-live-survey (never individually re-confirmed)
[HYP] Cross-tenant BOLA across tenant/user/membership/association-request endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /user/{id}/, /association-request/{id}/, /user/eav/
confidence: 60
reasoning: EdgePortal DRF multi-tenancy exposes per-tenant user/membership/relationship/EAV + bulk export with sequential object IDs; per-tenant authorization is the sole BOLA chokepoint (401 when unauthenticated, WWW-Authenticate: Token). New surface this cycle: association-request, user-token, tenant/self, inventory/tenant/self — all IDs sequential and auth-gated. No new unauth vector found after drilling schema/config/sites paths (all SPA fallback or 401). Registration/signup path not yet identified — if one exists, it could provide legitimate credential acquisition.
evidence_needed: authenticated low-priv account reading another tenant's user/membership/relationship/association-request objects by ID; OR a registration/signup endpoint that allows self-service account creation
verify_steps: (1) GET https://pluto.portal.ipb.de/api/ and inspect SPA bundle for signup/register/join routes; (2) GET https://pluto.portal.ipb.de/signup/ and /register/ and /join/ — check for account creation flow; (3) If signup found: create low-priv account; POST /api/session/ {username,password} to obtain Token; GET /api/multi-tenancy/v1/tenant/{id}/, /user/{id}/, /association-request/{id}/ across tenant boundaries
impact: cross-tenant PII dump / privilege escalation — HIGH
testability: AUTH_HELPED
[HYP] Unprobed inventory hosts contain live services behind wildcard DNS
class: MISCONFIG
asset: eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, survey.ipb.de, webcam.ipb.de, speedtest.ipb.de, cic.ipb.de, www.cic.ipb.de, www.survey.ipb.de
confidence: 45
reasoning: Initial seed probed all 33 hosts and found 0 live HTTP; later 3 were confirmed live (pluto, event, www) and 5 confirmed DNS-fail (app, auth.gold, my, prod, cloud). The remaining 21 hosts were assumed dead but never individually re-confirmed in documented probe results. Hosts like eticket (ticketing), nc (Nextcloud), gold.* (tier system), piwik (analytics) represent distinct services that could have been provisioned since 2026-09-02. Wildcard DNS on *.ipb.de means any subdomain resolves to the same IP — services could exist behind reverse proxy with Host-based routing.
evidence_needed: HTTP 200 from any previously-unconfirmed host showing distinct content (not default/catch-all page)
verify_steps: HEAD https://eticket.ipb.de/ then HEAD https://nc.ipb.de/ then HEAD https://gold.ipb.de/ then HEAD https://piwik.ipb.de/ then HEAD https://survey.ipb.de/ then HEAD https://webcam.ipb.de/ then HEAD https://speedtest.ipb.de/ then HEAD https://cic.ipb.de/ then HEAD https://www.cic.ipb.de/ then HEAD https://www.survey.ipb.de/ — all at 1 rps HEAD only; flag any non-error response or distinct Server header
impact: discovering new live attack surface — potentially HIGH depending on service
testability: PASSIVE
[HYP] EdgePortal SPA registration/signup endpoint enables self-service credential acquisition
class: AUTH
asset: pluto.portal.ipb.de /signup/, /register/, /join/, /create-account/ or SPA bundle routes
confidence: 40
reasoning: EdgePortal is a multi-tenant customer portal (DRF + React SPA). Customer portals typically have self-service registration or invite-based signup. The SPA bundle (354606 bytes) has not been fully enumerated for auth-related routes. If signup exists, it provides a path to legitimate credentials for BOLA testing — removing the HUMAN gate. DRF often includes /api/auth/registration/ or /api/users/registration/ by default.
evidence_needed: any GET response from registration-related paths showing account creation form or API
verify_steps: GET https://pluto.portal.ipb.de/signup/ then GET https://pluto.portal.ipb.de/register/ then GET https://pluto.portal.ipb.de/join/ then GET https://pluto.portal.ipb.de/api/auth/registration/ then GET https://pluto.portal.ipb.de/api/users/ — check for signup/registration endpoints
impact: removes HUMAN gate on BOLA testing; enables full auth-hypothesis exercise — HIGH (enabling)
testability: PASSIVE
[PARKED] Check-in kiosk TOTP/user enumeration (45): POST-only endpoint, needs token-format knowledge, WAF/rate-limit risk, low severity alone. Cannot advance without valid token format.
[PARKED] Admin dynamic-config mass-assignment (45): needs elevated admin creds, no path to obtain — gated on same human sponsor as BOLA.
[PARKED] Kiosk_login token oracle (45): unchanged from prior cycles, low alone, WAF risk.
[PARKED] event.ipb.de pretix-broken-auth (4.9): fully saturated — /control 403, /redirect allowlisted, mature hardening confirmed across 4 cycles. Reject.
[PARKED] www.ipb.de Plesk-static (3.0): .env/server-info 403 blocked across 3 cycles. No further surface. Reject.
[FINAL] Cross-tenant BOLA incl. association-request/user-token/user-eav surfaces (60) — highest confidence, HIGH impact, but HUMAN-gated on credentials
[FINAL] Unprobed inventory hosts (45) — potentially testable NOW via HEAD requests, could reveal new attack surface
[FINAL] EdgePortal registration endpoint discovery (40) — if signup exists, removes HUMAN gate entirely; highest leverage action this cycle
[NEXT] PROBE: HEAD https://eticket.ipb.de/ then HEAD https://nc.ipb.de/ then HEAD https://gold.ipb.de/ then HEAD https://piwik.ipb.de/ then HEAD https://survey.ipb.de/ then HEAD https://webcam.ipb.de/ then HEAD https://speedtest.ipb.de/ then HEAD https://cic.ipb.de/ (1 rps, HEAD only) — check for live HTTP from 8 previously-unconfirmed inventory hosts. In parallel: GET https://pluto.portal.ipb.de/signup/ and GET https://pluto.portal.ipb.de/register/ and GET https://pluto.portal.ipb.de/api/auth/registration/ — check for self-service account creation on EdgePortal.
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: EdgePortal multi-tenancy is prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access. Unchanged this cycle.
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan; 21 inventory hosts never individually re-confirmed post-discovery. Potential for hidden services behind wildcard proxy.
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle. PARKED due to low impact + WAF risk.
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /schema, /swagger, /openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface.
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked).
[RISK] ipb: 55 — EdgePortal pluto.portal.ipb.de exposes a very large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI, association-request). Surface high but uniformly gated by Token auth. Unauth risk moderate (only /api/ct/ CSRF token + login/405 endpoints exposed). 21 inventory hosts may harbor undiscovered live services behind wildcard DNS. Highest exposure materializes once any credentialed account is obtained — currently HUMAN-gated, but registration endpoint discovery could change this.
## 2026-09-04 06:50:09 UTC [target] (model bigpickle)
[PRIO] pluto.portal.ipb.de,7.20 — attack_surface=9, business_value=10, tech_exposure=8, gate_ease=5, cloud_surface=6, freshness=10
[PRIO] eticket.ipb.de,4.80 — SSL cert fail = live behind wildcard proxy
[PRIO] event.ipb.de,4.90 — pretix mature hardened, saturated
[PRIO] nc.ipb.de,4.20 — SSL cert fail, Nextcloud = auth+upload+WebDAV
[PRIO] www.ipb.de,3.00 — Plesk static, config blocked
[HYP] Self-service registration endpoints bypass HUMAN credential gate
class: AUTH
asset: pluto.portal.ipb.de /_exceptions/user-register/, /_exceptions/register-tenant/
confidence: 55
reasoning: SPA bundle contains these routes distinct from 403 API-level registration endpoints; /signup/ and /register/ are SPA fallback (354606 bytes); _exceptions paths may be frontend forms
evidence_needed: GET response showing form HTML (not SPA fallback)
verify_steps: GET /_exceptions/user-register/ then /_exceptions/register-tenant/ then /_exceptions/forgot-password/ — check Content-Length ≠ 354606
impact: removes HUMAN gate on BOLA — HIGH (enabling)
testability: PASSIVE
[HYP] SSL-cert-fail hosts hide live services behind reverse proxy
class: MISCONFIG
asset: eticket, nc, gold, piwik, webcam, cic.ipb.de
confidence: 50
reasoning: 6 hosts returned SSL cert verify failures = live (TCP open, TLS attempted), cert mismatch from wildcard proxy; HTTP port 80 or --insecure may reveal service
evidence_needed: HTTP 200 from http:// or HTTPS with --insecure showing distinct content
verify_steps: curl -k on each host; also try http:// port 80
impact: discovering new live attack surface — potentially HIGH
testability: PASSIVE
[NEXT] PROBE: GET https://pluto.portal.ipb.de/_exceptions/user-register/ then GET /_exceptions/register-tenant/ then GET /_exceptions/forgot-password/ — highest-leverage passive probe; if registration form exists, removes HUMAN gate on top BOLA hypothesis.
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: _exceptions routes in SPA bundle are most promising path for self-service credential acquisition
[LEARN] ACCEPTED MISCONFIG @ eticket/nc/gold/piwik/webcam/cic.ipb.de: SSL cert failures confirm live hosts behind wildcard proxy
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ allowlisted
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 blocked
[RISK] ipb: 58 (↑ from 55) — EdgePortal 70+ auth-gated endpoints + 6 SSL-cert-fail hosts live behind wildcard proxy + registration routes could remove HUMAN gate. Validated bugs: 0.
## 2026-09-04 11:53:43 UTC [target] (model bigpickle)
[PRIO] pluto.portal.ipb.de,7.20 — attack_surface=9, business_value=10, tech_exposure=8, gate_ease=5, cloud_surface=6, freshness=10
[PRIO] eticket.ipb.de,4.80 — SSL cert fail = live behind wildcard proxy
[PRIO] nc.ipb.de,4.20 — SSL cert fail, Nextcloud = auth+upload+WebDAV
[PRIO] event.ipb.de,4.90 — pretix mature hardened, saturated
[PRIO] www.ipb.de,3.00 — Plesk static, config blocked
[HYP] EdgePortal _exceptions registration routes enable self-service credential acquisition
class: AUTH
asset: pluto.portal.ipb.de /_exceptions/user-register/, /_exceptions/register-tenant/
confidence: 55
reasoning: SPA bundle contains these routes distinct from 403 API-level registration; /signup/ and /register/ are SPA fallback (354606 bytes); _exceptions paths may be frontend forms
evidence_needed: GET response showing form HTML (not SPA fallback)
verify_steps: GET /_exceptions/user-register/ then /_exceptions/register-tenant/ then /_exceptions/forgot-password/ — check Content-Length ≠ 354606
impact: removes HUMAN gate on BOLA — HIGH (enabling)
testability: PASSIVE
[HYP] SSL-cert-fail hosts hide live services behind reverse proxy
class: MISCONFIG
asset: eticket, nc, gold, piwik, webcam, cic.ipb.de
confidence: 50
reasoning: 6 hosts returned SSL cert verify failures = live (TCP open, TLS attempted), cert mismatch from wildcard proxy; HTTP port 80 or --insecure may reveal service
evidence_needed: HTTP 200 from http:// or HTTPS with --insecure showing distinct content
verify_steps: curl -k on each host; also try http:// port 80
impact: discovering new live attack surface — potentially HIGH
testability: PASSIVE
[HYP] Cross-tenant BOLA via sequential IDs on multi-tenancy endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/
confidence: 65
reasoning: DRF multi-tenancy with sequential IDs uniformly auth-gated; per-tenant auth is sole BOLA control; association-request, user-token, self endpoints all use seq-ID
evidence_needed: authenticated request showing cross-tenant data access
verify_steps: HUMAN_ONLY — requires valid Token to test
impact: cross-tenant PII/business data dump — CRITICAL
testability: HUMAN_ONLY
[PARKED] Cross-tenant BOLA (65): highest confidence + impact but HUMAN_ONLY — no path to credentials without registration discovery. Parked until registration probe resolves.
[FINAL] EdgePortal registration discovery (55) — PASSIVE, high leverage, removes HUMAN gate
[FINAL] SSL-cert-fail hosts (50) — PASSIVE, could reveal new attack surface
[NEXT] PROBE: GET https://pluto.portal.ipb.de/_exceptions/user-register/ then GET /_exceptions/register-tenant/ then GET /_exceptions/forgot-password/ — check Content-Length ≠ 354606 (SPA fallback = 354606 bytes). If distinct response, registration form exists and HUMAN gate is removable. Concurrently: curl -k https://eticket.ipb.de/ then https://nc.ipb.de/ then https://gold.ipb.de/ — check for live HTTP content behind wildcard proxy.
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: _exceptions routes in SPA bundle are most promising path for self-service credential acquisition
[LEARN] ACCEPTED MISCONFIG @ eticket/nc/gold/piwik/webcam/cic.ipb.de: SSL cert failures confirm live hosts behind wildcard proxy
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ allowlisted
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 blocked
[RISK] ipb: 58 (↑ from 55) — EdgePortal 70+ auth-gated endpoints + 6 SSL-cert-fail hosts live behind wildcard proxy + registration routes could remove HUMAN gate. Validated bugs: 0.
## 2026-09-04 15:28:36 UTC [target] (model bigpickle)
[NEW] nc.ipb.de — Nextcloud 34.0.3 live behind wildcard proxy; nginx/1.28.3 (Ubuntu); status.php leaks full version/install status; OCS API + WebDAV + provisioning_api + impersonate + oauth2 + circles apps enabled
[NEW] gold.ipb.de — Jitsi Meet live behind wildcard proxy; nginx/1.22.1; config.js publicly accessible; anonymous guest domain configured (guest.gold.ipb.de); XMPP backend (auth.gold.ipb.de)
[NEW] piwik.ipb.de — Plesk Panel "I/P/B/ Cloudhosting Panel" 1800260901.22 live (303→/login.php); forgery_protection_token in HTML meta
[NEW] webcam.ipb.de — Plesk Panel "I/P/B/ Cloudhosting Panel" 1800260901.22 live (303→/login.php); same Plesk instance as piwik
[NEW] cic.ipb.de — Customer Information Center (CIC) live; custom PHP login form (User ID + Password); PHPSESSID cookie; nginx/1.31.4
[NEW] eticket.ipb.de — Pretix "Unknown host" (400); pretix instance handles wildcard but no custom domain configured
[CHANGED] pluto.portal.ipb.de/_exceptions/user-register/ returns SPA fallback (354606 bytes) — no distinct registration form; _exceptions/register-tenant/ and _exceptions/forgot-password/ identical
[CHANGED] pluto.portal.ipb.de/api/multi-tenancy/v1/user-registration/ returns 401 (auth-gated, not public self-registration)
[CHANGED] pluto.portal.ipb.de/api/multi-tenancy/v1/tenant-registration/ returns 403 Forbidden (admin-only or disabled)
[PRIO] nc.ipb.de,8.15 — attack_surface=8, business_value=10, tech_exposure=9, gate_ease=6, cloud_surface=5, freshness=9 — Nextcloud 34.0.3 with provisioning_api, impersonate, oauth2, circles, WebDAV, OCS API; status.php version leak; all data endpoints auth-gated
[PRIO] pluto.portal.ipb.de,7.20 — attack_surface=9, business_value=10, tech_exposure=8, gate_ease=5, cloud_surface=6, freshness=10 — unchanged; 70+ auth-gated endpoints; BOLA still top hypothesis pending auth
[PRIO] gold.ipb.de,6.85 — attack_surface=7, business_value=8, tech_exposure=7, gate_ease=7, cloud_surface=3, freshness=7 — Jitsi Meet with anonymous guest access, config.js leak, XMPP backend
[PRIO] cic.ipb.de,5.70 — attack_surface=5, business_value=9, tech_exposure=5, gate_ease=5, cloud_surface=2, freshness=5 — custom PHP CIC login, potential auth issues
[PRIO] piwik.ipb.de,5.80 — attack_surface=5, business_value=8, tech_exposure=6, gate_ease=5, cloud_surface=4, freshness=5 — Plesk Panel with exposed forgery_protection_token
[PRIO] webcam.ipb.de,5.80 — same as piwik.ipb.de
[PRIO] event.ipb.de,4.90 — pretix mature hardened, saturated
[PRIO] www.ipb.de,3.00 — Plesk static, config blocked
[HYP] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: status.php confirms Nextcloud 34.0.3 installed, not in maintenance; capabilities API confirms provisioning_api (user CRUD), impersonate (admin act-as-user), oauth2 (token-based auth), circles (group sharing) all enabled; WebDAV endpoint returns Sabre DAV error with detailed auth guidance; bruteforce delay=0 in capabilities
evidence_needed: authenticated OCS request showing user listing or impersonation; or unauth endpoint leaking user data
verify_steps: GET https://nc.ipb.de/ocs/v2.php/cloud/users (with token) to list users; POST /ocs/v2.php/cloud/users with impersonate app; check /ocs/v2.php/cloud/activity for unauth data; check /status.php is already info leak (version 34.0.3)
impact: full user data access + impersonation of any user — CRITICAL if auth obtained
testability: HUMAN_ONLY
[HYP] Plesk Panel forgery_protection_token is static/reusable for CSRF
class: AUTH
asset: piwik.ipb.de, webcam.ipb.de
confidence: 45
reasoning: both Plesk instances expose forgery_protection_token in HTML meta tag (piwik: b4bfe2ae17bfcdab6c0eb22f216e0ed8, webcam: 486e8124f2b4926317c408be3e4a51f9); Plesk 18.0.80-6 build; if token is static per session or predictable, CSRF on login or admin actions possible
evidence_needed: second request showing same token value (static) or predictable pattern; POST to login with token succeeding
verify_steps: GET https://piwik.ipb.de/login.php twice, compare forgery_protection_token; check /login.php POST with token in body
impact: CSRF on Plesk admin panel — HIGH (admin takeover)
testability: PASSIVE (token re-read confirms staticity)
[HYP] Custom PHP CIC login vulnerable to SQL injection
class: AUTH
asset: cic.ipb.de
confidence: 35
reasoning: custom PHP login form (not a known CMS/framework), User ID + Password fields, PHPSESSID cookie, no visible framework; custom PHP = higher chance of injection; but no evidence of actual vuln
evidence_needed: error message revealing SQL syntax or database type on malformed input
verify_steps: GET https://cic.ipb.de/ with input "user"=' OR 1=1-- in POST; but OUT OF SCOPE per rules (brute-force, SQLi on login panels without confirmed path)
impact: account takeover — CRITICAL
testability: PASSIVE
[PARKED] CIC SQLi (35): custom PHP login is interesting but no evidence of vulnerability; also "public login panels" is out-of-scope per program rules; confidence <40
[PARKED] Plesk forgery_protection_token (45): token may rotate per session; need staticity confirmation; Plesk panels also potentially out-of-scope as "public login panels"
[FINAL] nc.ipb.de Nextcloud provisioning + impersonate (55): highest new value, HUMAN_ONLY but well-defined
[FINAL] pluto.portal.ipb.de cross-tenant BOLA (65): unchanged, HUMAN_ONLY — still top overall
[NEXT] PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, oauth2 scope; also GET https://nc.ipb.de/ocs/v2.php/cloud/activity and GET https://nc.ipb.de/ocs/v2.php/cloud/user with empty credentials — check for unauthenticated data leak. Concurrently: GET https://piwik.ipb.de/login.php twice and compare forgery_protection_token to confirm staticity.
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix responds with "Unknown host" — no custom domain configured, pretix handles wildcard
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all return SPA fallback (354606 bytes) = no server-side registration form; API endpoints auth-gated (user-registration 401, tenant-registration 403); self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api, impersonate, oauth2, circles, WebDAV, OCS API; bruteforce delay=0; status.php full version leak
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet with config.js public, anonymous guest domain (guest.gold.ipb.de), XMPP backend (auth.gold.ipb.de)
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token in meta tag
[RISK] ipb: 68 (↑ from 58) — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with provisioning_api + impersonate is CRITICAL value if auth obtained; gold.ipb.de Jitsi with anonymous access; cic.ipb.de custom PHP login; two Plesk panels. pluto.portal.ipb.de remains top BOLA target. _exceptions registration hypothesis killed. Validated bugs: 0.
[NEW] nc.ipb.de — Nextcloud 34.0.3 live behind wildcard proxy (curl -k); nginx/1.28.3 (Ubuntu); status.php full version leak
[NEW] gold.ipb.de — Jitsi Meet live behind wildcard proxy; nginx/1.22.1; config.js public; anonymous guest domain (guest.gold.ipb.de), XMPP backend (auth.gold.ipb.de)
[NEW] piwik.ipb.de — Plesk Panel 18.0.80-6 "I/P/B/ Cloudhosting Panel" live; forgery_protection_token in HTML meta
[NEW] webcam.ipb.de — Plesk Panel 18.0.80-6 live (same instance as piwik)
[NEW] cic.ipb.de — Customer Information Center live; custom PHP login (User ID + Password); PHPSESSID
[NEW] eticket.ipb.de — pretix responds "Unknown host" (400) — no custom domain configured for eticket
[CHANGED] pluto.portal.ipb.de/_exceptions/{user-register,register-tenant,forgot-password} all SPA fallback (354606) — no server-side registration form
[CHANGED] pluto user-registration API 401 (auth-gated), tenant-registration 403 (admin-only) — no self-service credential path
[PRIO] nc.ipb.de,8.15 — attack_surface=8, business_value=10, tech_exposure=9, gate_ease=6, cloud_surface=5, freshness=9 — Nextcloud with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS
[PRIO] pluto.portal.ipb.de,7.20 — attack_surface=9, business_value=10, tech_exposure=8, gate_ease=5, cloud_surface=6, freshness=10 — BOLA top overall (unchanged)
[PRIO] gold.ipb.de,6.85 — attack_surface=7, business_value=8, tech_exposure=7, gate_ease=7, cloud_surface=3, freshness=7 — Jitsi anonymous guest
[PRIO] piwik.ipb.de,5.80 — attack_surface=5, business_value=8, tech_exposure=6, gate_ease=5, cloud_surface=4, freshness=5 — Plesk Panel
[PRIO] webcam.ipb.de,5.80 — same as piwik
[PRIO] cic.ipb.de,5.70 — custom PHP login
[PRIO] event.ipb.de,4.90 — pretix saturated
[PRIO] www.ipb.de,3.00 — static Plesk, config blocked
[HYP] Nextcloud provisioning_api + impersonate = privilege escalation
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: status.php confirms Nextcloud 34.0.3 installed, not in maintenance; capabilities API confirms provisioning_api, impersonate, oauth2, circles, WebDAV enabled; bruteforce delay=0; /remote.php/dav/ Sabre DAV NotAuthenticated
evidence_needed: authenticated OCS showing user listing or unauth data leak
verify_steps: GET /ocs/v2.php/cloud/capabilities (full app list); GET /ocs/v2.php/cloud/activity; GET /ocs/v2.php/cloud/user — all unauthenticated
impact: full user data + impersonate any user — CRITICAL if auth obtained
testability: HUMAN_ONLY
[HYP] Plesk forgery_protection_token static → CSRF on admin panel
class: AUTH
asset: piwik.ipb.de, webcam.ipb.de
confidence: 45
reasoning: hash-like token in login page meta (piwik b4bfe2ae..., webcam 486e8124f2...); Plesk 18.0.80-6; distinct per host
evidence_needed: second GET returns identical token (staticity)
verify_steps: GET /login.php twice, diff forgery_protection_token
impact: CSRF on Plesk admin — HIGH if token static
testability: PASSIVE
[PARKED] cic CIC SQLi(35): public login panel out-of-scope + no evidence; drop
[PARKED] Plesk forgery token(45): staticity unproven, Plesk panel is public-login scope edge; needs passive confirmation
[FINAL] nc.ipb.de Nextcloud provisioning+impersonate (55) — HUMAN_ONLY but CRITICAL value
[FINAL] pluto.portal.ipb.de cross-tenant BOLA (65) — top overall
[NEXT] PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities (full enabled-app confirmation) then GET https://nc.ipb.de/ocs/v2.php/cloud/activity and GET https://nc.ipb.de/ocs/v2.php/cloud/user unauthenticated — check for data leak; concurrently GET https://piwik.ipb.de/login.php twice to diff forgery_protection_token staticity.
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services behind wildcard proxy (Nextcloud 34.0.3, Jitsi Meet, Plesk x2, custom CIC PHP)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[RISK] ipb: 68 (↑ from 58) — 5 new live services behind wildcard proxy; nc Nextcloud provisioning+impersonate CRITICAL-if-auth; gold Jitsi anonymous guest; 2 Plesk panels; custom CIC login. _exceptions self-reg killed (no credential path). Validated bugs: 0.
## 2026-09-04 18:33:11 UTC [target] (model bigpickle)
## 2026-09-04 21:00:36 UTC [target] (model bigpickle)
