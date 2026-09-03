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
