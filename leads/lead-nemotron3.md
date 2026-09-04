## 2026-09-03 16:37:04 UTC [target] (model nemotron3)
[PRIO] app.ipb.de,0,attack_surface=0,business_value=8,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] auth.gold.ipb.de,0,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] cloud.ipb.de,0,attack_surface=0,business_value=8,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] my.ipb.de,0,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] prod.ipb.de,0,attack_surface=0,business_value=8,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] cic.ipb.de,0,attack_surface=0,business_value=7,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] gold.ipb.de,0,attack_surface=0,business_value=7,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] speedtest.ipb.de,0,attack_surface=0,business_value=5,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] www.ipb.de,0,attack_surface=0,business_value=6,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] ipb.de,0,attack_surface=0,business_value=6,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[HYP] Wildcard DNS masks real attack surface
class: MISCONFIG
asset: *.ipb.de
confidence: 60
reasoning: Dedicated deep scan found 0 genuinely dedicated hosts; all 33 inventory hosts resolve to shared/CDN/wildcard IPs. Real services may exist behind wildcard but not enumerated passively.
evidence_needed: HTTP responses from any host showing distinct service fingerprints (Server header, app routes, tech stack) proving non-wildcard content
verify_steps: GET https://app.ipb.de/ | HEAD https://auth.gold.ipb.de/ | GET https://cloud.ipb.de/ | GET https://my.ipb.de/ | GET https://prod.ipb.de/ (all with Host header, 1 rps, GET/HEAD only)
impact: If real services exist behind wildcard, full attack surface (auth, cloud, customer portal, prod API) is hidden from passive recon
testability: PASSIVE
[HYP] Customer portal at my.ipb.de exposes IDOR/BOLA on account resources
class: IDOR
asset: my.ipb.de
confidence: 35
reasoning: Naming pattern "my." suggests customer self-service portal; such portals commonly have object-level auth flaws on /api/v1/account/{id}, /subscription/{id}, /invoice/{id} endpoints
evidence_needed: Live HTTP 200 on my.ipb.de with authenticated session showing user-scoped resource IDs in responses
verify_steps: GET https://my.ipb.de/ (check for login portal, API endpoints in JS, versioned /api/ paths)
impact: Cross-tenant access to customer PII, billing, subscription data — HIGH severity
testability: AUTH_HELPED
[HYP] auth.gold.ipb.de OAuth/OIDC implementation vulnerable to redirect_uri validation bypass
class: OATH
asset: auth.gold.ipb.de
confidence: 30
reasoning: "auth.gold" subdomain suggests centralized auth provider (possibly Keycloak/Auth0/custom); OAuth redirect_uri flaws are high-value for ATO via code theft
evidence_needed: Live HTTP on auth.gold.ipb.de showing /authorize, /token, /.well-known/openid-configuration endpoints
verify_steps: GET https://auth.gold.ipb.de/.well-known/openid-configuration | GET https://auth.gold.ipb.de/authorize?client_id=test&redirect_uri=https://evil.com&response_type=code
impact: Full account takeover via authorization code theft — CRITICAL severity
testability: PASSIVE
[PARKED] Customer portal at my.ipb.de exposes IDOR/BOLA on account resources: confidence 35 < 40, no live endpoint confirmed, verify_steps require auth which we don't have
[PARKED] auth.gold.ipb.de OAuth/OIDC implementation vulnerable to redirect_uri validation bypass: confidence 30 < 40, no live endpoint confirmed, passive verify may 404
[FINAL] Wildcard DNS masks real attack surface (confidence 60) — only surviving hypothesis
[NEXT] PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de at 1 rps GET/HEAD only
[LEARN] REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
[LEARN] REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
[RISK] ipb: 15 — wildcard-dominated DNS with 0 confirmed live HTTP endpoints; attack surface currently unobservable. Risk increases to 60+ once any live customer-facing portal (my.ipb.de, auth.gold.ipb.de, cloud.ipb.de) is confirmed.
## 2026-09-03 19:31:56 UTC [target] (model nemotron3)
[NEW] pluto.portal.ipb.de live — React SPA backed by DRF multi-tenancy API (HTTP 200)
[NEW] event.ipb.de live — pretix ticketing (HTTP 200), nginx/1.31.4
[NEW] www.ipb.de live — PHP/8.3.33 on PleskLin (HTTP 200)
[NEW] ipb.de → 301 → www.ipb.de
[CHANGED] `/.env`, `/server-info` on www.ipb.de → 403 (blocked, not exposed)
[NEW] app.ipb.de, auth.gold.ipb.de, my.ipb.de, prod.ipb.de, cloud.ipb.de — DNS resolution failure (Name or service not known) or SSL cert verify failed on probe attempts
[PRIO] pluto.portal.ipb.de,7.75,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=7,freshness=9
[PRIO] event.ipb.de,5.45,attack_surface=5,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=3,freshness=8
[PRIO] www.ipb.de,4.35,attack_surface=3,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7
[PRIO] auth.gold.ipb.de,2.25,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] my.ipb.de,2.25,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] cloud.ipb.de,2.00,attack_surface=0,business_value=8,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[HYP] Cross-tenant BOLA across membership/tenant/user endpoints on pluto.portal.ipb.de
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/
confidence: 60
reasoning: DRF multi-tenancy exposes per-tenant user/membership/relationship objects with EAV + bulk export endpoints; object IDs are sequential; per-tenant authorization is the prime BOLA chokepoint. All endpoints return `Authentication credentials were not provided` unauthenticated.
evidence_needed: authenticated low-priv account reading another tenant's user/membership/relationship objects by ID
verify_steps: POST /api/session/ {username,password} to obtain session; GET /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/ across tenant boundaries as low-priv user
impact: cross-tenant PII dump / privilege escalation — HIGH
testability: AUTH_HELPED
[HYP] Admin dynamic-config mass assignment override via /api/admin/dynamic-config/v1/config-namespace/apply/
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
## 2026-09-03 21:55:13 UTC [target] (model nemotron3)
[NEW] pluto.portal.ipb.de live — React SPA backed by DRF multi-tenancy API (HTTP 200), extensive /api/multi-tenancy/v1/, /api/admin/, /api/check-in/ endpoints discovered in bundle  
[NEW] event.ipb.de live — pretix ticketing (HTTP 200), nginx/1.31.4, /control 403, /redirect/ allowlisted  
[NEW] www.ipb.de live — PHP/8.3.33 on PleskLin (HTTP 200)  
[NEW] ipb.de → 301 → www.ipb.de  
[CHANGED] `/.env`, `/server-info` on www.ipb.de → 403 (blocked, not exposed)  
[NEW] app.ipb.de, auth.gold.ipb.de, my.ipb.de, prod.ipb.de, cloud.ipb.de — DNS resolution failure (Name or service not known) or SSL cert verify failed on probe attempts  
[CHANGED] Dedicated deep scan confirms wildcard DNS on *.ipb.de — 0 genuinely dedicated hosts after shared-IP filtering  
[PRIO] pluto.portal.ipb.de,7.75,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=7,freshness=9  
[PRIO] event.ipb.de,5.45,attack_surface=5,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=3,freshness=8  
[PRIO] www.ipb.de,4.35,attack_surface=3,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7  
[PRIO] auth.gold.ipb.de,2.25,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0  
[PRIO] my.ipb.de,2.25,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0  
[PRIO] cloud.ipb.de,2.00,attack_surface=0,business_value=8,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0  
[HYP] Cross-tenant BOLA across membership/tenant/user endpoints on pluto.portal.ipb.de  
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/  
confidence: 60  
reasoning: DRF multi-tenancy exposes per-tenant user/membership/relationship objects with EAV + bulk export endpoints; object IDs are sequential; per-tenant authorization is the sole BOLA control. All endpoints return `Authentication credentials were not provided` unauthenticated.  
evidence_needed: authenticated low-priv account reading another tenant's user/membership/relationship objects by ID  
verify_steps: POST /api/session/ {username,password} to obtain session; GET /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/ across tenant boundaries as low-priv user  
impact: cross-tenant PII dump / privilege escalation — HIGH  
testability: AUTH_HELPED  
[HYP] Check-in kiosk token user-enumeration via /api/check-in/v1/check-in/kiosk_login/  
class: AUTH  
asset: pluto.portal.ipb.de /api/check-in/v1/check-in/kiosk_login/  
confidence: 45  
reasoning: POST {token} returns `No User matches the given query.` vs other responses for invalid tokens — token existence oracle; 32-hex token space; no rate limit observed in bundle.  
evidence_needed: enumerate valid 32-hex kiosk tokens without auth  
verify_steps: POST /api/check-in/v1/check-in/kiosk_login/ with candidate 32-hex tokens; observe response differentiation (status/body)  
impact: low alone, weakens kiosk gate; enables targeted token brute-force — LOW-MEDIUM  
testability: PASSIVE  
[HYP] Wildcard DNS masks real attack surface on *.ipb.de  
class: MISCONFIG  
asset: *.ipb.de (app.ipb.de, auth.gold.ipb.de, my.ipb.de, prod.ipb.de, cloud.ipb.de)  
confidence: 60  
reasoning: Dedicated deep scan found 0 genuinely dedicated hosts; all 33 inventory hosts resolve to shared/CDN/wildcard IPs. Real services may exist behind wildcard but not enumerated passively.  
evidence_needed: HTTP responses from any host showing distinct service fingerprints (Server header, app routes, tech stack) proving non-wildcard content  
verify_steps: GET https://app.ipb.de/ | HEAD https://auth.gold.ipb.de/ | GET https://cloud.ipb.de/ | GET https://my.ipb.de/ | GET https://prod.ipb.de/ (all with Host header, 1 rps, GET/HEAD only)  
impact: If real services exist behind wildcard, full attack surface (auth, cloud, customer portal, prod API) is hidden from passive recon  
testability: PASSIVE  
[PARKED] Admin dynamic-config mass assignment override via /api/admin/dynamic-config/v1/config-namespace/apply/: confidence 45 but requires admin/elevated creds (HUMAN_ONLY gate), no path to obtain creds  
[FINAL] Cross-tenant BOLA across membership/tenant/user endpoints (confidence 60) — highest confidence, clear verify path, HIGH impact  
[FINAL] Wildcard DNS masks real attack surface (confidence 60) — only surviving passive hypothesis, testable now  
[FINAL] Check-in kiosk token user-enumeration (confidence 45) — passive testable, but token format knowledge required; low impact alone  
[NEXT] PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de at 1 rps GET/HEAD only  
[LEARN] REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth  
[LEARN] REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface  
[LEARN] ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/  
[LEARN] REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist  
[LEARN] ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/  
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)  
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending credentialed access  
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle  
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe  
[LEARN] ACCEPTED cross-tenant BOLA @ pluto.portal.ipb.de: DRF multi-tenancy API with sequential IDs, auth-gated but per-tenant auth is sole BOLA control  
[RISK] ipb: 55 — pluto.portal.ipb.de exposes an unusually large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI); surface is high but gated by auth. Unauthenticated risk moderate. Highest exposure emerges once any credentialed account is obtained. Wildcard DNS on *.ipb.de still masks potential additional attack surface (app, auth, cloud, my, prod subdomains unresolved).
## 2026-09-03 23:44:09 UTC [target] (model nemotron3)
[NEW] pluto.portal.ipb.de live — React SPA backed by DRF multi-tenancy API (HTTP 200), extensive /api/multi-tenancy/v1/, /api/admin/, /api/check-in/ endpoints discovered in bundle
[NEW] event.ipb.de live — pretix ticketing (HTTP 200), nginx/1.31.4, /control 403, /redirect/ allowlisted
[NEW] www.ipb.de live — PHP/8.3.33 on PleskLin (HTTP 200)
[NEW] ipb.de → 301 → www.ipb.de
[CHANGED] `/.env`, `/server-info` on www.ipb.de → 403 (blocked, not exposed)
[NEW] app.ipb.de, auth.gold.ipb.de, my.ipb.de, prod.ipb.de, cloud.ipb.de — DNS resolution failure (Name or service not known) or SSL cert verify failed on probe attempts
[CHANGED] Dedicated deep scan confirms wildcard DNS on *.ipb.de — 0 genuinely dedicated hosts after shared-IP filtering
[PRIO] pluto.portal.ipb.de,7.75,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=7,freshness=9
[PRIO] event.ipb.de,5.45,attack_surface=5,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=3,freshness=8
[PRIO] www.ipb.de,4.35,attack_surface=3,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7
[PRIO] auth.gold.ipb.de,2.25,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] my.ipb.de,2.25,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] cloud.ipb.de,2.00,attack_surface=0,business_value=8,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[HYP] Cross-tenant BOLA across membership/tenant/user endpoints on pluto.portal.ipb.de
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/ and /api/multi-tenancy/v1/user/{id}/
confidence: 60
reasoning: DRF multi-tenancy exposes per-tenant user/membership/relationship objects with EAV + bulk export endpoints; object IDs are sequential; per-tenant authorization is the sole BOLA control. All endpoints return `Authentication credentials were not provided` unauthenticated.
evidence_needed: authenticated low-priv account reading another tenant[0m
impact: If real services exist behind wildcard, full attack surface (auth, cloud, customer portal, prod API) is hidden from passive recon
testability: PASSIVE
[HYP] Check-in kiosk token user-enumeration via /api/check-in/v1/check-in/kiosk_login/
class: AUTH
asset: pluto.portal.ipb.de /api/check-in/v1/check-in/kiosk_login/
confidence: 45
reasoning: POST {token} returns `No User matches the given query.` vs other responses for invalid tokens — token existence oracle; 32-hex token space; no rate limit observed in bundle.
evidence_needed: enumerate valid 32-hex kiosk tokens without auth
verify_steps: POST /api/check-in/v1/check-in/kiosk_login/ with candidate 32-hex tokens; observe response differentiation (status/body)
impact: low alone, weakens kiosk gate; enables targeted token brute-force — LOW-MEDIUM
testability: PASSIVE
[PARKED] Admin dynamic-config mass assignment override via /api/admin/dynamic-config/v1/config-namespace/apply/: confidence 45 but requires admin/elevated creds (HUMAN_ONLY gate), no path to obtain creds
[PARKED] Check-in kiosk token user-enumeration: confidence 45 but endpoint requires valid token format knowledge; enumeration at scale may trigger WAF
[FINAL] Cross-tenant BOLA across membership/tenant/user endpoints (confidence 60) — highest confidence, clear verify path, HIGH impact
[FINAL] Wildcard DNS masks real attack surface (confidence 60) — only surviving passive hypothesis, testable now
[FINAL] Check-in kiosk token user-enumeration (confidence 45) — passive testable, but token format knowledge required; low impact alone
[NEXT] PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de at 1 rps GET/HEAD only
[LEARN] REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
[LEARN] REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
[LEARN] ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
[LEARN] REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
[LEARN] ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending credentialed access
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED cross-tenant BOLA @ pluto.portal.ipb.de: DRF multi-tenancy API with sequential IDs, auth-gated but per-tenant auth is sole BOLA control
[RISK] ipb: 55 — pluto.portal.ipb.de exposes an unusually large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI); surface is high but gated by auth. Unauthenticated risk moderate. Highest exposure emerges once any credentialed account is obtained. Wildcard DNS on *.ipb.de still masks potential additional attack surface (app, auth, cloud, my, prod subdomains unresolved).
## 2026-09-04 01:57:40 UTC [target] (model nemotron3)
[NEW] pluto.portal.ipb.de bundle reveals additional endpoints: /api/system/ (live DRF, 401 Token-auth), /api/check-in/v1/check-in/kiosk_totp/validate/ (POST-only), /api/multi-tenancy/v1/association-request/
[CHANGED] /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/docs, /api/openapi all return SPA fallback (200 text/html) — no DRF schema/OpenAPI exposure
[CHANGED] /api/system/ confirmed real DRF endpoint (401) not SPA fallback
[NEW] App name confirmed: "EdgePortal" (title in index.html)
[PRIO] pluto.portal.ipb.de,8.15,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=2,cloud_surface=7,freshness=10
[PRIO] event.ipb.de,5.45,attack_surface=5,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=3,freshness=8
[PRIO] www.ipb.de,4.35,attack_surface=3,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7
[PRIO] app.ipb.de,2.50,attack_surface=1,business_value=7,tech_exposure=1,gate_ease=1,cloud_surface=2,freshness=5
[PRIO] auth.gold.ipb.de,2.25,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] my.ipb.de,2.25,attack_surface=0,business_value=9,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] cloud.ipb.de,2.00,attack_surface=0,business_value=8,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[PRIO] prod.ipb.de,2.00,attack_surface=0,business_value=8,tech_exposure=0,gate_ease=0,cloud_surface=0,freshness=0
[HYP] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/check-in/v1/check-in/, /api/multi-tenancy/v1/association-request/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request objects with sequential integer IDs; all endpoints auth-gated (401) but per-tenant authorization is sole BOLA control; expanded surface includes association-request, user-token, self endpoints; EdgePortal bundle confirms extensive API surface
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation
verify_steps: 1) Obtain low-priv credentialed session; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with valid auth; 3) Observe 200 vs 403/404 differentiation
impact: Cross-tenant PII dump (membership, user, association-request data); HIGH
testability: AUTH_HELPED
[HYP] Kiosk TOTP validation endpoint logic flaw
class: AUTH
asset: pluto.portal.ipb.de /api/check-in/v1/check-in/kiosk_totp/validate/
confidence: 40
reasoning: POST-only endpoint for TOTP validation; kiosk_login already shows token oracle (distinct error per token validity); TOTP validation may have timing/response differentiation or reusable codes
evidence_needed: response differentiation between valid/invalid TOTP codes, or replayable codes
verify_steps: 1) POST /api/check-in/v1/check-in/kiosk_totp/validate/ with candidate TOTP codes; 2) Observe status/body/timing differences; 3) Test code reuse
impact: Kiosk authentication bypass; MEDIUM
testability: PASSIVE
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de
class: MISCONFIG
asset: *.ipb.de (app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 6 subdomains resolve but fail HTTP/SSL probe; real services may exist behind wildcard responding only to correct Host headers or specific paths
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://app.ipb.de/ (Host: app.ipb.de) — check Server header, content length, status vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de at 1 rps GET/HEAD only
impact: Hidden attack surface (auth, cloud, customer portal, prod API) — HIGH if real services exist
testability: PASSIVE
[PARKED] Kiosk TOTP validation endpoint logic flaw: confidence 40 (below 40 threshold), requires valid token format knowledge, enumeration at scale may trigger WAF
[FINAL] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in endpoints (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact
[FINAL] Wildcard DNS masking hidden attack surface on *.ipb.de (confidence 60) — only surviving passive hypothesis, testable now, HIGH impact if confirmed
[NEXT] PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de at 1 rps GET/HEAD only
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[RISK] ipb: 60 — pluto.portal.ipb.de exposes large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI, association-request); surface high but gated by auth. Unauthenticated risk moderate. Highest exposure emerges once any credentialed account obtained. Wildcard DNS on *.ipb.de masks potential additional attack surface (app, auth, cloud, my, prod subdomains unresolved). Risk increased from 55 due to expanded API surface confirmation.
## 2026-09-04 06:54:25 UTC [target] (model nemotron3)
[NEW] pluto.portal.ipb.de bundle confirms /api/system/ (live DRF, 401 Token), /api/check-in/v1/check-in/kiosk_totp/validate/ (POST-only), /api/multi-tenancy/v1/association-request/
[NEW] App name confirmed: "EdgePortal" (title in index.html)
[CHANGED] /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/docs, /api/openapi all return SPA fallback (200 text/html) — no DRF schema/OpenAPI exposure
[CHANGED] Wildcard DNS deep scan: 21 inventory hosts never individually re-confirmed post-discovery; potential hidden services behind wildcard proxy
[CHANGED] app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de — DNS resolution failure or SSL cert verify failed on probe attempts (persistent)
[NEW] eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de — SSL cert verify failed on probe attempts
[NEW] survey.ipb.de, www.cic.ipb.de — DNS resolution failure on probe attempts
[NEW] speedtest.ipb.de — timeout on probe attempt
[PRIO] pluto.portal.ipb.de,8.15,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=2,cloud_surface=7,freshness=10
[PRIO] *.ipb.de (wildcard),6.80,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=8,cloud_surface=8,freshness=8
[PRIO] event.ipb.de,5.45,attack_surface=5,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=3,freshness=8
[PRIO] www.ipb.de,4.35,attack_surface=3,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7
[PRIO] eticket.ipb.de,3.20,attack_surface=2,business_value=6,tech_exposure=2,gate_ease=5,cloud_surface=3,freshness=5
[PRIO] nc.ipb.de,3.20,attack_surface=2,business_value=6,tech_exposure=2,gate_ease=5,cloud_surface=3,freshness=5
[PRIO] gold.ipb.de,3.20,attack_surface=2,business_value=6,tech_exposure=2,gate_ease=5,cloud_surface=3,freshness=5
[PRIO] piwik.ipb.de,3.20,attack_surface=2,business_value=6,tech_exposure=2,gate_ease=5,cloud_surface=3,freshness=5
[PRIO] webcam.ipb.de,3.20,attack_surface=2,business_value=6,tech_exposure=2,gate_ease=5,cloud_surface=3,freshness=5
[PRIO] cic.ipb.de,3.20,attack_surface=2,business_value=6,tech_exposure=2,gate_ease=5,cloud_surface=3,freshness=5
[HYP] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/check-in/v1/check-in/, /api/multi-tenancy/v1/association-request/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request objects with sequential integer IDs; all endpoints auth-gated (401) but per-tenant authorization is sole BOLA control; expanded surface includes association-request, user-token, self endpoints; EdgePortal bundle confirms extensive API surface
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation
verify_steps: 1) Obtain low-priv credentialed session; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with valid Authorization: Token header; 3) Observe 200 vs 403/404 differentiation
impact: Cross-tenant PII dump (membership, user, association-request data); HIGH
testability: AUTH_HELPED
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains
class: MISCONFIG
asset: *.ipb.de (app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; real services may exist behind wildcard responding only to correct Host headers or specific paths; probe results show SSL cert failures on several subdomains suggesting live TLS endpoints
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://app.ipb.de/ (Host: app.ipb.de) — check Server header, content length, status vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
impact: Hidden attack surface (auth, cloud, customer portal, prod API, monitoring, ticketing) — HIGH if real services exist
testability: PASSIVE
[HYP] Kiosk TOTP validation endpoint logic flaw
class: AUTH
asset: pluto.portal.ipb.de /api/check-in/v1/check-in/kiosk_totp/validate/
confidence: 40
reasoning: POST-only endpoint for TOTP validation; kiosk_login already shows token oracle (distinct error per token validity); TOTP validation may have timing/response differentiation or reusable codes
evidence_needed: response differentiation between valid/invalid TOTP codes, or replayable codes
verify_steps: 1) POST /api/check-in/v1/check-in/kiosk_totp/validate/ with candidate TOTP codes; 2) Observe status/body/timing differences; 3) Test code reuse
impact: Kiosk authentication bypass; MEDIUM
testability: PASSIVE
[PARKED] Kiosk TOTP validation endpoint logic flaw: confidence 40 (at threshold), requires valid token format knowledge, enumeration at scale may trigger WAF, low impact relative to cross-tenant BOLA
[FINAL] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact
[FINAL] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed
[NEXT] PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard fallback; repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[RISK] ipb: 60 — pluto.portal.ipb.de exposes large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI, association-request); surface high but gated by auth. Unauthenticated risk moderate. Highest exposure emerges once any credentialed account obtained. Wildcard DNS on *.ipb.de masks potential additional attack surface (app, auth, cloud, my, prod, eticket, nc, gold, piwik, webcam, cic subdomains unresolved or SSL cert failures suggesting live TLS endpoints). Risk increased from 55 due to expanded API surface confirmation and wildcard proxy potential.
