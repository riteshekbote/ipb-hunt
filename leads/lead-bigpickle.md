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
## 2026-09-04 22:54:05 UTC [target] (model bigpickle)
[HYP] Cross-tenant BOLA on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de
confidence: 65
reasoning: DRF multi-tenant portal, all data endpoints seq-ID auth-gated; per-tenant token auth is sole cross-tenant control; /api/system/ live 401 confirms real surface still up
evidence_needed: two tenant accounts; object access with token from tenant A against tenant B seq-id
verify_steps: authenticated GET on /api/multi-tenancy/v1/<obj>/<seq> cross-tenant
impact: cross-tenant PII/association data dump — CRITICAL
testability: HUMAN_ONLY
[HYP] Nextcloud provisioning + impersonate mis-scoping
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: live caps confirm NC 34.0.3, bruteforce delay=0; OCS alive; impersonate/provisioning apps reported enabled in bundle; unauth leak now ruled out (997), so impact requires any valid session
evidence_needed: authed OCS /cloud/users + /cloud/user/{id} impersonation test
verify_steps: GET /ocs/v2.php/cloud/users?format=json with valid token; impersonate header
impact: full user data + account takeover as any user — CRITICAL
testability: HUMAN_ONLY
[HYP] Jitsi anonymous guest room access
class: AUTH
asset: gold.ipb.de
confidence: 35
reasoning: config.js shows anonymousdomain guest.gold.ipb.de and public websocket/BOSH; rooms unguessable random strings; anonymous join is deliberate config
evidence_needed: any real meeting room URL leaks or lobby bypass
verify_steps: GET /{room}/info static assets; no leak path evident
impact: eavesdrop if room URL known — by-design exposure
testability: HUMAN_ONLY
## 2026-09-05 00:30:36 UTC [target] (model bigpickle)
[HYP] Cross-tenant BOLA on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de
confidence: 65
reasoning: DRF multi-tenant portal; /api/system/ live 401; all seq-ID object endpoints auth-gated; per-tenant token auth is sole cross-tenant control; no public registration route (SPA fallback, user-reg 401, tenant-reg 403)
evidence_needed: two tenant accounts; tenant-A token accessing tenant-B sequential object IDs
verify_steps: authenticated GET /api/multi-tenancy/v1/<obj>/<seq> iterate seq ids with cross-tenant token
impact: cross-tenant PII/association data dump — CRITICAL
testability: HUMAN_ONLY
[HYP] Nextcloud authenticated provisioning/impersonate mis-scoping
class: AUTH
asset: nc.ipb.de
confidence: 45
reasoning: NC 34.0.3 live; caps confirm app_api 34.0.0 + bruteforce delay=0; activity+user OCS return 401/997 unauth; provisioning/impersonate enablement NOT confirmed by caps endpoint (earlier bundle claim unsupported by live caps); unauth leak ruled out
evidence_needed: any valid NC session; OCS /cloud/users listing + impersonate header test
verify_steps: GET /ocs/v2.php/cloud/users?format=json with token; replay with OC-Impersonate header
impact: full user data + account takeover as any user — CRITICAL if session obtained
testability: HUMAN_ONLY
[NEXT] HUMAN: obtain one attacker-owned tenant account on pluto.portal.ipb.de EdgePortal and test token-scoped cross-tenant seq-ID access on /api/multi-tenancy/v1/{association-request,user,check-in}/* against a sibling tenant; also obtain a valid low-priv NC session for /ocs/v2.php/cloud/users + impersonate header.
[RISK] ipb: 60 (↓ from 68) — piwik token staticity disproven, piwik/webcam reduced to shared default vhost (risk overcount corrected), nc unauth leak closed, caps contradict earlier impersonate/provisioning claim; remaining exposure: NC34 hosted file surface, Plesk panel default-vhost blast radius, wildcard hides services. Validated bugs: 0.
## 2026-09-05 05:00:45 UTC [target] (model bigpickle)
[HYP] Cross-tenant BOLA on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{tenant,user,association-request,user-token,membership}/
confidence: 65
reasoning: DRF multi-tenant portal; GET probes uniformly 401 Token-auth; per-tenant token is sole cross-tenant control. This cycle additionally confirmed public OPTIONS metadata exposes full seq-ID serializer schemas (user, tenant) useful for exact IDOR BOLA tests.
evidence_needed: two tenant accounts; tenant-A token reading tenant-B seq-ID objects
verify_steps: POST /api/session/ {username,password} → GET /api/multi-tenancy/v1/user/{id}/, /tenant/{id}/, /association-request/{id}/ cross-tenant
impact: cross-tenant PII/association dump — CRITICAL
testability: HUMAN_ONLY
[HYP] Nextcloud OCS provisioning/impersonate mis-scoping
class: AUTH
asset: nc.ipb.de
confidence: 45
reasoning: NC 34.0.3 live, bruteforce delay=0; unauth activity/user OCS closed (401/997); live caps do NOT confirm impersonate/provisioning enablement (contradicts earlier bundle claim); any session would unlock /cloud/users + OC-Impersonate tests.
evidence_needed: any valid low-priv NC session
verify_steps: GET /ocs/v2.php/cloud/users?format=json with token; replay with OC-Impersonate header against /cloud/user/{id}
impact: full user data + ATO — CRITICAL if session obtained
testability: HUMAN_ONLY
[HYP] Profile-picture upload content-type confusion → stored XSS cross-tenant
class: OTHER
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/
confidence: 45
reasoning: live POST multipart upload endpoint (401 unauth = auth-gated); avatar served via /download/ on same origin as admin SPA; SVG/HTML content-type bypass or filename reuse could yield stored XSS against staff viewing a tenant's avatar; multi-tenant makes attacker reach any staff tenant.
evidence_needed: authed upload of SVG/HTML avatar served inline from portal origin
verify_steps: with any tenant token POST multipart file (svg/html) → GET /download/ observe Content-Type + inline rendering
impact: stored XSS → admin session theft → full multi-tenant compromise — HIGH
testability: HUMAN_ONLY
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant account (pluto.portal.ipb.de) — then, using the now-known public OPTIONS schemas, test cross-tenant seq-ID BOLA on /api/multi-tenancy/v1/{user,tenant,association-request,user-token}/(1..N) with the tenant-A token, AND test profile-picture upload/ with an SVG payload (stored-XSS chain). Also obtain one low-priv NC session for /ocs/v2.php/cloud/users + OC-Impersonate. All three FINAL hypotheses are purely credential-gated.
[RISK] ipb: 60 (unchanged) — no validated bugs; genuinely new this cycle: 1 dedicated live host (mirror, nginx 404 low value), public unauth reset endpoint + DRF metadata schema leak on pluto (recon value, recovery-policy OOS), auth-gated avatar upload surface. Credential gate remains the single blocker for all CRITICAL-valued hypotheses (BOLA 65, NC 45, upload 45). Validated bugs: 0.
## 2026-09-05 08:46:29 UTC [target] (model bigpickle)
[HYP] Cross-tenant BOLA on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{tenant,user,association-request,user-token,membership}/
confidence: 65
reasoning: DRF multi-tenant portal; all object GET probes uniformly 401 Token-auth; public OPTIONS metadata exposes seq-ID serializer schemas; per-tenant token is sole cross-tenant control; no public reg route (SPA fallback, user-reg 401, tenant-reg 403)
evidence_needed: two tenant accounts; tenant-A token reading tenant-B seq-ID objects
verify_steps: POST /api/session/ {username,password} -> GET /api/multi-tenancy/v1/{user,tenant,association-request}/{id}/ cross-tenant (200 vs 403/404)
impact: cross-tenant PII/property dump — CRITICAL
testability: HUMAN_ONLY
[HYP] NC AppAPI (ExApps) mis-scoping on honest capability set
class: AUTH
asset: nc.ipb.de ocs/v2.php/cloud/capabilities + app_api 34.0.0
confidence: 40
reasoning: live caps (curl -k, 200) confirm NC 34.0.3, bruteforce.delay=0, app_api 34.0.0; provisioning/impersonate NOT confirmed by live caps (prior KC overstated); app_api on NC34 enables external app runtime historically exposing auth/SSRF issues — needs session
evidence_needed: any valid NC session; AppAPI registered ExApps list + their endpoints
verify_steps: with token GET /ocs/v2.php/apps/app_api/apps/list; check unauth /apps/app_api/ endpoints
impact: external app runtime mis-scoping — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] Profile-picture upload content-type confusion -> stored XSS cross-tenant
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/
confidence: 45
reasoning: POST multipart upload endpoint auth-gated (401); avatar served via /download/ on admin SPA origin; SVG/HTML content-type bypass or filename reuse could yield stored XSS against staff; multi-tenant = attacker reaches any staff tenant
evidence_needed: authed upload of SVG/HTML avatar served inline from portal origin
verify_steps: with any tenant token POST multipart (svg/html) -> GET /download/ observe Content-Type + inline render
impact: stored XSS -> admin session theft -> full multi-tenant compromise — HIGH
testability: HUMAN_ONLY
[PARKED] Jitsi anonymous guest room access: by-design config, unguessable random roomName, no room-URL leak path, confidence 35, HUMAN_ONLY, low impact
[PARKED] CIC custom PHP login: plain user+pass form, no CSRF token, but public login panel = out-of-scope class; no evidenced vuln, confidence 30
[PARKED] NC bruteforce.delay=0: brute-force/rate-limit policy = out-of-scope class
[FINAL] pluto.portal.ipb.de Cross-tenant BOLA (65) — highest value, credential-gated
[FINAL] pluto.portal.ipb.de avatar upload stored-XSS (45) — credential-gated, high impact chain
[FINAL] nc.ipb.de AppAPI mis-scoping (40) — credential-gated, corrected capability basis
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) then (a) test cross-tenant seq-ID BOLA on /api/multi-tenancy/v1/{user,tenant,association-request}/(1..N) with tenant-A token, (b) POST avatar upload/ with SVG payload -> /download/ for stored-XSS chain. Also obtain one low-priv NC session to enumerate /ocs/v2.php/apps/app_api/apps/list.
[LEARN] CHANGED framework-recon @ nc.ipb.de: live OCS capabilities (curl -k, 200) shows NC 34.0.3 with bruteforce.delay=0 and app_api 34.0.0 ONLY; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ cic.ipb.de: self-hosted CIC, plain custom PHP user+pass self-POST form, no CSRF token, PHPSESSID; login-only (out-of-scope class)
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000) this cycle — Jitsi anonymousdomain config-only, not a separate live vhost
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi root confirms unguessable random roomName rooms; anonymous guest by-design; no room-URL leak path
[LEARN] REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persists, no new hidden service
[RISK] ipb: 58 (down from 60) — live caps correction removes overstated provision/impersonate claim from risk basis; guest.gold non-vhost, CIC/login panels out-of-scope, remaining wildcard hosts DNS-dead; remaining exposure: NC34 hosted-file + app_api surface (auth-gated), Plesk default-vhost blast radius, pluto multi-tenant BOLA (top, 65, credential-gated). Validated bugs: 0.
[HYP] Cross-tenant BOLA on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{tenant,user,association-request,user-token,membership}/
confidence: 65
reasoning: DRF multi-tenant portal; all object GET probes uniformly 401 Token-auth; public OPTIONS metadata exposes seq-ID serializer schemas; per-tenant token is sole cross-tenant control; no public reg route (SPA fallback, user-reg 401, tenant-reg 403)
evidence_needed: two tenant accounts; tenant-A token reading tenant-B seq-ID objects
verify_steps: POST /api/session/ {username,password} -> GET /api/multi-tenancy/v1/{user,tenant,association-request}/{id}/ cross-tenant (200 vs 403/404)
impact: cross-tenant PII/property dump — CRITICAL
testability: HUMAN_ONLY
[HYP] NC AppAPI (ExApps) mis-scoping on honest capability set
class: AUTH
asset: nc.ipb.de ocs/v2.php/cloud/capabilities + app_api 34.0.0
confidence: 40
reasoning: live caps (curl -k, 200) confirm NC 34.0.3, bruteforce.delay=0, app_api 34.0.0; provisioning/impersonate NOT confirmed by live caps (prior KC overstated); app_api on NC34 enables external app runtime historically exposing auth/SSRF issues — needs session
evidence_needed: any valid NC session; AppAPI registered ExApps list + their endpoints
verify_steps: with token GET /ocs/v2.php/apps/app_api/apps/list; check unauth /apps/app_api/ endpoints
impact: external app runtime mis-scoping — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] Profile-picture upload content-type confusion -> stored XSS cross-tenant
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/
confidence: 45
reasoning: POST multipart upload endpoint auth-gated (401); avatar served via /download/ on admin SPA origin; SVG/HTML content-type bypass or filename reuse could yield stored XSS against staff; multi-tenant = attacker reaches any staff tenant
evidence_needed: authed upload of SVG/HTML avatar served inline from portal origin
verify_steps: with any tenant token POST multipart (svg/html) -> GET /download/ observe Content-Type + inline render
impact: stored XSS -> admin session theft -> full multi-tenant compromise — HIGH
testability: HUMAN_ONLY
[PARKED] Jitsi anonymous guest room access: by-design config, unguessable random roomName, no room-URL leak path, confidence 35, HUMAN_ONLY, low impact
[PARKED] CIC custom PHP login: plain user+pass form, no CSRF token, but public login panel = out-of-scope class; no evidenced vuln, confidence 30
[PARKED] NC bruteforce.delay=0: brute-force/rate-limit policy = out-of-scope class
[FINAL] pluto.portal.ipb.de Cross-tenant BOLA (65) — highest value, credential-gated
[FINAL] pluto.portal.ipb.de avatar upload stored-XSS (45) — credential-gated, high impact chain
[FINAL] nc.ipb.de AppAPI mis-scoping (40) — credential-gated, corrected capability basis
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) then (a) test cross-tenant seq-ID BOLA on /api/multi-tenancy/v1/{user,tenant,association-request}/(1..N) with tenant-A token, (b) POST avatar upload/ with SVG payload -> /download/ for stored-XSS chain. Also obtain one low-priv NC session to enumerate /ocs/v2.php/apps/app_api/apps/list.
[LEARN] CHANGED framework-recon @ nc.ipb.de: live OCS capabilities (curl -k, 200) shows NC 34.0.3 with bruteforce.delay=0 and app_api 34.0.0 ONLY; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ cic.ipb.de: self-hosted CIC, plain custom PHP user+pass self-POST form, no CSRF token, PHPSESSID; login-only (out-of-scope class)
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000) this cycle — Jitsi anonymousdomain config-only, not a separate live vhost
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi root confirms unguessable random roomName rooms; anonymous guest by-design; no room-URL leak path
[LEARN] REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persists, no new hidden service
[RISK] ipb: 58 (down from 60) — live caps correction removes overstated provision/impersonate claim from risk basis; guest.gold non-vhost, CIC/login panels out-of-scope, remaining wildcard hosts DNS-dead; remaining exposure: NC34 hosted-file + app_api surface (auth-gated), Plesk default-vhost blast radius, pluto multi-tenant BOLA (top, 65, credential-gated). Validated bugs: 0.
## 2026-09-05 12:13:36 UTC [target] (model bigpickle)
[HYP] NC AppAPI ExApp auth mis-scoping (extend prior, 404 not 401)
class: AUTH
asset: nc.ipb.de ocs/v2.php/apps/app_api/apps/list (+ app_api ExApp registry endpoints)
confidence: 40
reasoning: unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (not 401); prior caps confirmed NC 34.0.3, app_api 34.0.0 active, bruteforce.delay=0; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered
evidence_needed: any valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed
verify_steps: GET /ocs/v2.php/cloud/capabilities (200, done); with token GET /ocs/v2.php/apps/app_api/apps/list; unauth GET each returned ExApp route
impact: external app auth/SSRF mis-scoping — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] pluto cross-tenant BOLA (unchanged)
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request}/
confidence: 65
reasoning: all object GET probes uniformly 401 Token-auth; OPTIONS exposes seq-ID schemas; per-tenant token sole cross-tenant control; no self-service credential path (SPA fallback, user-reg 401, tenant-reg 403) — unchanged this cycle
verify_steps: two tenant accounts; tenant-A token GET tenant-B seq-ID objects (200 vs 403/404)
impact: cross-tenant PII/property dump — CRITICAL
testability: HUMAN_ONLY
[HYP] pluto avatar upload stored-XSS (unchanged)
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/ + /download/
confidence: 45
reasoning: authed POST multipart; avatar served inline from portal origin; SVG/HTML content-type bypass could yield stored XSS vs staff across tenants
verify_steps: any tenant token POST svg/html multipart → GET /download/ check Content-Type + inline render
impact: stored XSS → admin session theft → full multi-tenant compromise — HIGH
testability: HUMAN_ONLY
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) and one low-priv NC session, then: (a) cross-tenant seq-ID BOLA on /api/multi-tenancy/v1/{user,tenant,association-request}/(1..N) with tenant-A token; (b) POST avatar upload/ SVG → /download/ stored-XSS chain; (c) enumerate /ocs/v2.php/apps/app_api/apps/list + probe returned ExApp routes unauth.
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 and /ocs/v2.php/cloud/apps → 401 — AppAPI ExApp list and provisioning both require a session; confirms NC AppAPI lead is session-gated (404 routing, not unauth leak).
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: both Plesk login.php → 303 (unchanged); public login panel = out-of-scope class; no new attack surface.
[RISK] ipb: 58 (unchanged) — no new validated bug this cycle; all remaining high-value leads (pluto BOLA 65, avatar stored-XSS 45, NC AppAPI 40) are credential-gated with zero validated bugs; live surface fully surveyed, hard targets only.
## 2026-09-05 15:17:27 UTC [target] (model bigpickle)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.40,attack_surface=7,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=8,freshness=9
[PRIO] gold.ipb.de,5.35,attack_surface=5,business_value=6,tech_exposure=5,gate_ease=8,cloud_surface=4,freshness=6
[PRIO] piwik/webcam.ipb.de,4.20,attack_surface=4,business_value=5,tech_exposure=4,gate_ease=3,cloud_surface=4,freshness=5
[PRIO] cic.ipb.de,3.85,attack_surface=3,business_value=5,tech_exposure=3,gate_ease=5,cloud_surface=3,freshness=5
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request}/{id}/
confidence: 65
reasoning: DRF multi-tenancy exposes tenant/user/association-request objects with sequential integer IDs; all 401 Token-auth unauthenticated; per-tenant Token is sole cross-tenant authorization control; OPTIONS on endpoints exposes seq-ID field schemas; expanded surface (association-request, user-token, self, check-in) all auth-gated with seq-IDs; no self-service credential acquisition path (SPA fallback on _exceptions, user-reg 401, tenant-reg 403)
evidence_needed: two tenant accounts; tenant-A Token requesting tenant-B objects by sequential ID — 200 vs 403/404 differentiation
verify_steps: POST /api/session/ {username,password} to obtain Token; GET /api/multi-tenancy/v1/user/{id}/ with Authorization: Token header across tenant boundary; repeat for /tenant/{id}/, /association-request/{id}/
impact: cross-tenant PII dump (user records, membership, association-request data) — CRITICAL
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry mis-scoping (session-gated, 404 not 401)
class: AUTH
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS capabilities (curl -k, 200) confirmed NC 34.0.3, bruteforce.delay=0, app_api 34.0.0 ONLY; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps (prior KC overstated); unauth GET /ocs/v2.php/apps/app_api/apps/list returns 404 (not 401) indicating routing exists but empty/no-session; NC AppAPI 34.0.0 enables external app runtime historically exposing auth bypass/SSRF once ExApps are registered; /ocs/v2.php/cloud/apps → 401 confirms session required for other OCS lists
evidence_needed: any valid NC session; enumerate ExApps list then probe each ExApp route unauth vs authed
verify_steps: with valid NC session GET /ocs/v2.php/apps/app_api/apps/list; check each returned ExApp's registered routes unauthenticated
impact: external app runtime auth/SSRF mis-scoping — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] EdgePortal avatar upload stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/ + /download/
confidence: 45
reasoning: POST multipart upload endpoint auth-gated (401); avatar served via /download/ endpoint on portal SPA origin; SVG/HTML content-type bypass or filename-extension confusion could yield stored XSS served to admin/staff across tenants; multi-tenant = any attacker tenant reaches staff viewing profiles
evidence_needed: authed upload of SVG/HTML payload served inline from portal origin with wrong Content-Type
verify_steps: with any tenant Token POST multipart file (svg/html) → GET /download/ observe Content-Type header + inline render behavior
impact: stored XSS → admin session theft → full multi-tenant compromise — HIGH
testability: HUMAN_ONLY
[PARKED] CIC custom PHP login: public login panel = out-of-scope class per scope.yml; confidence 30; no evidence of injection
[PARKED] Plesk forgery_protection_token CSRF: public login panel edge; token staticity unproven via automated probe (Python urllib SSL-fail); out-of-scope class borderline; confidence 45 but no verify path
[PARKED] NC bruteforce.delay=0: rate-limit policy class = out-of-scope per program rules
[PARKED] Jitsi anonymous guest: by-design, unguessable random roomName, no leak path, confidence 35
[FINAL] 1. pluto.portal.ipb.de Cross-tenant BOLA (65) — highest confidence + impact, credential-gated
[FINAL] 2. nc.ipb.de AppAPI mis-scoping (40) — at threshold, credential-gated, corrected capability basis
[FINAL] 3. pluto.portal.ipb.de avatar stored-XSS (45) — credential-gated, high chain value
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) then (a) cross-tenant seq-ID BOLA on /api/multi-tenancy/v1/{user,tenant,association-request}/(1..N) with tenant-A token, (b) POST avatar upload/ with SVG payload → /download/ for stored-XSS chain. Also obtain one low-priv NC session to enumerate /ocs/v2.php/apps/app_api/apps/list.
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access. Unchanged this cycle.
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is low-severity enumeration; distinct error message per token validity confirmed in bundle. PARKED — low impact, WAF risk.
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live via OCS capabilities; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; /ocs/v2.php/apps/app_api/apps/list → 404 (session-gated routing)
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 login.php → 303; public login panel = out-of-scope class
[LEARN] ACCEPTED framework-recon @ cic.ipb.de: self-hosted CIC, custom PHP login, no CSRF token, PHPSESSID; login-only out-of-scope class
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject class
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist; mature hardening; do not re-probe
[LEARN] REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000); wildcard mask persists
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" (400); no custom domain configured
[RISK] ipb: 58 (unchanged) — no new validated bug this cycle; all remaining high-value leads (pluto BOLA 65, avatar XSS 45, NC AppAPI 40) are credential-gated; zero validated bugs; live surface fully surveyed; 58 reflects the large auth-gated attack surface that materializes once any valid account is obtained.
