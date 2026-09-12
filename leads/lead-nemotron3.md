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
## 2026-09-04 11:58:10 UTC [target] (model nemotron3)
[NEW] pluto.portal.ipb.de/_exceptions/user-register/ returns 200 (SPA fallback, 354606 bytes) — self-service registration route exists in bundle
[NEW] eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de — SSL cert verify failed (live TLS endpoints behind wildcard proxy)
[NEW] survey.ipb.de, www.cic.ipb.de — DNS resolution failure
[NEW] speedtest.ipb.de — timeout on probe
[CHANGED] Wildcard DNS deep scan: 21 inventory hosts never individually re-confirmed post-discovery; potential hidden services behind wildcard proxy
[CHANGED] app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de — persistent DNS resolution failure or SSL cert verify failed
[CHANGED] /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/docs, /api/openapi all confirmed SPA fallback (no DRF schema leak)
[PRIO] pluto.portal.ipb.de,8.15,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=2,cloud_surface=7,freshness=10
[PRIO] *.ipb.de (wildcard),6.80,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=8,cloud_surface=8,freshness=8
[PRIO] eticket.ipb.de,4.20,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] nc.ipb.de,4.20,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] gold.ipb.de,4.20,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] piwik.ipb.de,4.20,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] webcam.ipb.de,4.20,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] cic.ipb.de,4.20,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] event.ipb.de,3.95,attack_surface=4,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7
[PRIO] www.ipb.de,3.35,attack_surface=2,business_value=4,tech_exposure=2,gate_ease=8,cloud_surface=1,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/check-in/v1/check-in/, /api/multi-tenancy/v1/association-request/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request objects with sequential integer IDs; all endpoints auth-gated (401) but per-tenant authorization is sole BOLA control; expanded surface includes association-request, user-token, self endpoints; EdgePortal bundle confirms extensive API surface
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation
verify_steps: 1) Obtain low-priv credentialed session; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with valid Authorization: Token header; 3) Observe 200 vs 403/404 differentiation
impact: Cross-tenant PII dump (membership, user, association-request data); HIGH
testability: AUTH_HELPED
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de, app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content length, status vs wildcard; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[HYP] Self-service registration via _exceptions routes bypassing human credential gate
class: AUTH
asset: pluto.portal.ipb.de /_exceptions/user-register/, /_exceptions/register-tenant/, /_exceptions/forgot-password/
confidence: 45
reasoning: SPA bundle contains _exceptions routes (user-register returns 200 SPA fallback); these may be unprotected self-service registration/tenant-creation endpoints that bypass the normal human credential gate; EdgePortal multi-tenancy makes tenant registration a high-value target
evidence_needed: POST to registration endpoints returning 201/200 with account creation vs 403/404/redirect to auth
verify_steps: 1) GET /_exceptions/user-register/ — confirm not SPA fallback (check for form/API); 2) POST /_exceptions/user-register/ with minimal payload (email, password); 3) Observe response: account created, verification email sent, or auth required
impact: Unauthenticated tenant/account creation → credentialed access to multi-tenant API → cross-tenant BOLA chain; HIGH
testability: PASSIVE
[PARKED] Self-service registration via _exceptions routes bypassing human credential gate: confidence 45 (below 50 threshold), _exceptions routes return SPA fallback (200 text/html) not API endpoints, registration likely requires email verification or admin approval, low testability without knowing payload format
[FINAL] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[NEXT] PROBE: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content-length, status vs wildcard fallback; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[RISK] ipb: 60 — pluto.portal.ipb.de exposes large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI, association-request); surface high but gated by auth. Unauthenticated risk moderate. Highest exposure emerges once any credentialed account obtained. Wildcard DNS on *.ipb.de masks potential additional attack surface (6 subdomains with SSL cert failures confirm live TLS endpoints: eticket, nc, gold, piwik, webcam, cic; 5 more with DNS/SSL issues: app, auth.gold, cloud, my, prod). Risk increased from 55 due to expanded API surface confirmation and wildcard proxy potential with confirmed live TLS endpoints.
## 2026-09-04 15:30:53 UTC [target] (model nemotron3)
[NEW] pluto.portal.ipb.de/_exceptions/user-register/ returns 200 (SPA fallback, 354606 bytes) — self-service registration route confirmed in bundle
[NEW] eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de — SSL cert verify failed confirmed (live TLS endpoints behind wildcard proxy)
[NEW] survey.ipb.de, www.cic.ipb.de — DNS resolution failure
[NEW] speedtest.ipb.de — timeout on probe
[CHANGED] Wildcard DNS deep scan: 21 inventory hosts never individually re-confirmed post-discovery; potential hidden services behind wildcard proxy
[CHANGED] app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de — persistent DNS resolution failure or SSL cert verify failed
[CHANGED] /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/docs, /api/openapi all confirmed SPA fallback (no DRF schema leak)
[PRIO] pluto.portal.ipb.de,7.85,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=2,cloud_surface=7,freshness=10
[PRIO] *.ipb.de (wildcard),7.45,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=8,cloud_surface=8,freshness=8
[PRIO] eticket.ipb.de,4.45,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] nc.ipb.de,4.45,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] gold.ipb.de,4.45,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] piwik.ipb.de,4.45,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] webcam.ipb.de,4.45,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] cic.ipb.de,4.45,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] event.ipb.de,4.80,attack_surface=4,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7
[PRIO] www.ipb.de,3.70,attack_surface=2,business_value=4,tech_exposure=2,gate_ease=8,cloud_surface=1,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints
class: IDOR
asset: pluto.portal.ipb.de
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request objects with sequential integer IDs; all endpoints auth-gated (401) but per-tenant authorization is sole BOLA control; expanded surface includes association-request, user-token, self endpoints; EdgePortal bundle confirms extensive API surface
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation
verify_steps: 1) Obtain low-priv credentialed session; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with valid Authorization: Token header; 3) Observe 200 vs 403/404 differentiation
impact: Cross-tenant PII dump (membership, user, association-request data); HIGH
testability: AUTH_HELPED
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content length, status vs wildcard; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[HYP] Self-service registration via _exceptions routes bypassing human credential gate
class: AUTH
asset: pluto.portal.ipb.de /_exceptions/user-register/, /_exceptions/register-tenant/, /_exceptions/forgot-password/
confidence: 45
reasoning: SPA bundle contains _exceptions routes (user-register returns 200 SPA fallback); these may be unprotected self-service registration/tenant-creation endpoints that bypass the normal human credential gate; EdgePortal multi-tenancy makes tenant registration a high-value target
evidence_needed: POST to registration endpoints returning 201/200 with account creation vs 403/404/redirect to auth
verify_steps: 1) GET /_exceptions/user-register/ — confirm not SPA fallback (check for form/API); 2) POST /_exceptions/user-register/ with minimal payload (email, password); 3) Observe response: account created, verification email sent, or auth required
impact: Unauthenticated tenant/account creation → credentialed access to multi-tenant API → cross-tenant BOLA chain; HIGH
testability: PASSIVE
[PARKED] Self-service registration via _exceptions routes bypassing human credential gate: confidence 45 (below 50 threshold), _exceptions routes return SPA fallback (200 text/html) not API endpoints, registration likely requires email verification or admin approval, low testability without knowing payload format
[FINAL] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[NEXT] PROBE: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content-length, status vs wildcard fallback; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[RISK] ipb: 60 — pluto.portal.ipb.de exposes large authenticated multi-tenant API (PII, DNS, inventory, shipping, check-in, AI, association-request); surface high but gated by auth. Unauthenticated risk moderate. Highest exposure emerges once any credentialed account obtained. Wildcard DNS on *.ipb.de masks potential additional attack surface (6 subdomains with SSL cert failures confirm live TLS endpoints: eticket, nc, gold, piwik, webcam, cic; 5 more with DNS/SSL issues: app, auth.gold, cloud, my, prod). Risk increased from 55 due to expanded API surface confirmation and wildcard proxy potential with confirmed live TLS endpoints.
## 2026-09-04 18:43:18 UTC [target] (model nemotron3)
[NEW] nc.ipb.de — Nextcloud 34.0.3 live behind wildcard proxy; nginx/1.28.3; status.php full version leak; OCS API with provisioning_api, impersonate, oauth2, circles, WebDAV; bruteforce delay=0
[NEW] gold.ipb.de — Jitsi Meet live behind wildcard proxy; nginx/1.22.1; config.js public; anonymous guest domain (guest.gold.ipb.de); XMPP backend (auth.gold.ipb.de)
[NEW] piwik.ipb.de — Plesk Panel 18.0.80-6 "I/P/B/ Cloudhosting Panel" live (303→/login.php); forgery_protection_token in HTML meta
[NEW] webcam.ipb.de — Plesk Panel 18.0.80-6 live (same instance as piwik); forgery_protection_token in HTML meta
[NEW] cic.ipb.de — Customer Information Center live; custom PHP login (User ID + Password); PHPSESSID cookie; nginx/1.31.4
[NEW] eticket.ipb.de — Pretix "Unknown host" (400); pretix handles wildcard but no custom domain configured
[CHANGED] pluto.portal.ipb.de/_exceptions/{user-register,register-tenant,forgot-password} all SPA fallback (354606 bytes) — no server-side registration form; self-service credential hypothesis killed
[CHANGED] pluto.portal.ipb.de/api/multi-tenancy/v1/user-registration/ 401 (auth-gated); /tenant-registration/ 403 (admin-only)
[CHANGED] Wildcard DNS deep scan: 21 inventory hosts never individually re-confirmed; 6 subdomains with SSL cert failures confirm live TLS behind proxy
[CHANGED] app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de — persistent DNS resolution failure or SSL cert verify failed
[PRIO] nc.ipb.de,8.15,attack_surface=8,business_value=10,tech_exposure=9,gate_ease=6,cloud_surface=5,freshness=9
[PRIO] pluto.portal.ipb.de,7.85,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=2,cloud_surface=7,freshness=10
[PRIO] gold.ipb.de,6.85,attack_surface=7,business_value=8,tech_exposure=7,gate_ease=7,cloud_surface=3,freshness=7
[PRIO] piwik.ipb.de,5.80,attack_surface=5,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=4,freshness=5
[PRIO] webcam.ipb.de,5.80,attack_surface=5,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=4,freshness=5
[PRIO] cic.ipb.de,5.70,attack_surface=5,business_value=9,tech_exposure=5,gate_ease=5,cloud_surface=2,freshness=5
[PRIO] *.ipb.de (wildcard),7.45,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=8,cloud_surface=8,freshness=8
[PRIO] eticket.ipb.de,4.45,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] event.ipb.de,4.80,attack_surface=4,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7
[PRIO] www.ipb.de,3.70,attack_surface=2,business_value=4,tech_exposure=2,gate_ease=8,cloud_surface=1,freshness=6
[HYP] Cross-tenant BOLA via sequenti[0m
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation
verify_steps: 1) Obtain low-priv credentialed session; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with valid Authorization: Token header; 3) Observe 200 vs 403/404 differentiation
impact: Cross-tenant PII dump (membership, user, association-request data); HIGH
testability: AUTH_HELPED
[HYP] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: status.php confirms Nextcloud 34.0.3 installed, not in maintenance; capabilities API confirms provisioning_api (user CRUD), impersonate (admin act-as-user), oauth2 (token-based auth), circles (group sharing) all enabled; WebDAV endpoint returns Sabre DAV error with detailed auth guidance; bruteforce delay=0 in capabilities
evidence_needed: authenticated OCS request showing user listing or impersonation; or unauth endpoint leaking user data
verify_steps: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, oauth2 scope; GET https://nc.ipb.de/ocs/v2.php/cloud/activity and GET https://nc.ipb.de/ocs/v2.php/cloud/user with empty credentials — check for unauthenticated data leak
impact: Full user data access + impersonation of any user — CRITICAL if auth obtained
testability: HUMAN_ONLY
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content length, status vs wildcard; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[PARKED] Self-service registration via _exceptions routes bypassing human credential gate: confidence 45 (below 50 threshold), _exceptions routes return SPA fallback (354606 bytes) not API endpoints, registration API 401/403, no self-service credential path exists
[PARKED] Plesk forgery_protection_token static → CSRF on admin panel: confidence 45, token staticity unconfirmed, Plesk panel may be out-of-scope as public login panel
[PARKED] CIC custom PHP SQL injection: confidence 35, public login panel out-of-scope per program rules, no evidence of vulnerability
[FINAL] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector (confidence 55) — HUMAN_ONLY but CRITICAL value if auth obtained, well-defined verify steps
[FINAL] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[NEXT] PROBE: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content-length, status vs wildcard fallback; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with provisioning_api + impersonate is CRITICAL value if auth obtained; gold.ipb.de Jitsi with anonymous guest access; cic.ipb.de custom PHP login; two Plesk panels. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-04 21:05:08 UTC [target] (model nemotron3)
[NEW] nc.ipb.de/ocs/v2.php/cloud/capabilities probed (SSL cert fail) — OCS API surface unconfirmed live via probe-results
[NEW] piwik.ipb.de/login.php probed twice (SSL cert fail) — forgery_protection_token staticity unconfirmed
[CHANGED] pluto.portal.ipb.de/_exceptions/{user-register,register-tenant,forgot-password} all SPA fallback (354606 bytes) — self-service registration hypothesis killed (confirmed by both nemotron3 and bigpickle)
[CHANGED] pluto.portal.ipb.de/api/multi-tenancy/v1/user-registration/ 401, /tenant-registration/ 403 — no credential path
[CHANGED] 6 subdomains (eticket, nc, gold, piwik, webcam, cic) SSL cert verify failed — live TLS behind wildcard proxy confirmed
[CHANGED] 21 inventory hosts never individually re-confirmed post-discovery — wildcard DNS hides potential services
[CHANGED] app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de — persistent DNS/SSL failures
[CHANGED] event.ipb.de pretix /control 403 and /redirect/ allowlist — saturated, do not re-probe
[CHANGED] www.ipb.de .env/server-info 403 — saturated, do not re-probe
[PRIO] nc.ipb.de,8.15,attack_surface=8,business_value=10,tech_exposure=9,gate_ease=6,cloud_surface=5,freshness=9
[PRIO] pluto.portal.ipb.de,7.85,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=2,cloud_surface=7,freshness=10
[PRIO] gold.ipb.de,6.85,attack_surface=7,business_value=8,tech_exposure=7,gate_ease=7,cloud_surface=3,freshness=7
[PRIO] piwik.ipb.de,5.80,attack_surface=5,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=4,freshness=5
[PRIO] webcam.ipb.de,5.80,attack_surface=5,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=4,freshness=5
[PRIO] cic.ipb.de,5.70,attack_surface=5,business_value=9,tech_exposure=5,gate_ease=5,cloud_surface=2,freshness=5
[PRIO] *.ipb.de (wildcard),7.45,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=8,cloud_surface=8,freshness=8
[PRIO] eticket.ipb.de,4.45,attack_surface=3,business_value=6,tech_exposure=3,gate_ease=5,cloud_surface=4,freshness=6
[PRIO] event.ipb.de,4.80,attack_surface=4,business_value=5,tech_exposure=3,gate_ease=8,cloud_surface=2,freshness=7
[PRIO] www.ipb.de,3.70,attack_surface=2,business_value=4,tech_exposure=2,gate_ease=8,cloud_surface=1,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /user/{id}/, /association-request/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request objects with sequential integer IDs; all endpoints auth-gated (401) but per-tenant authorization is sole BOLA control; expanded surface includes association-request, user-token, self endpoints; EdgePortal bundle confirms extensive API surface
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation
verify_steps: 1) Obtain low-priv credentialed session; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with valid Authorization: Token header; 3) Observe 200 vs 403/404 differentiation
impact: Cross-tenant PII dump (membership, user, association-request data); HIGH
testability: AUTH_HELPED
[HYP] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: status.php confirms Nextcloud 34.0.3 installed, not in maintenance; capabilities API confirms provisioning_api (user CRUD), impersonate (admin act-as-user), oauth2 (token-based auth), circles (group sharing) all enabled; WebDAV endpoint returns Sabre DAV error with detailed auth guidance; bruteforce delay=0 in capabilities
evidence_needed: authenticated OCS request showing user listing or impersonation; or unauth endpoint leaking user data
verify_steps: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, oauth2 scope; GET https://nc.ipb.de/ocs/v2.php/cloud/activity and GET https://nc.ipb.de/ocs/v2.php/cloud/user with empty credentials — check for unauthenticated data leak
impact: Full user data access + impersonation of any user — CRITICAL if auth obtained
testability: HUMAN_ONLY
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content length, status vs wildcard; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[PARKED] Self-service registration via _exceptions routes bypassing human credential gate: confidence 45 (below 50 threshold), _exceptions routes return SPA fallback (354606 bytes) not API endpoints, registration API 401/403, no self-service credential path exists
[PARKED] Plesk forgery_protection_token static → CSRF on admin panel: confidence 45, token staticity unconfirmed, Plesk panel may be out-of-scope as public login panel
[PARKED] CIC custom PHP SQL injection: confidence 35, public login panel out-of-scope per program rules, no evidence of vulnerability
[FINAL] Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector (confidence 55) — HUMAN_ONLY but CRITICAL value if auth obtained, well-defined verify steps
[FINAL] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[NEXT] PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, oauth2 scope; GET https://nc.ipb.de/ocs/v2.php/cloud/activity and GET https://nc.ipb.de/ocs/v2.php/cloud/user with empty credentials — check for unauthenticated data leak
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with provisioning_api + impersonate is CRITICAL value if auth obtained; gold.ipb.de Jitsi with anonymous guest access; cic.ipb.de custom PHP login; two Plesk panels. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-04 22:53:14 UTC [target] (model nemotron3)
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /user/{id}/, /association-request/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request objects with sequential integer IDs; all endpoints auth-gated (401) but per-tenant authorization is sole BOLA control; expanded surface includes association-request, user-token, self endpoints; EdgePortal bundle confirms extensive API surface  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation  
verify_steps: 1) Obtain low-priv credentialed session; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with valid Authorization: Token header; 3) Observe 200 vs 403/404 differentiation  
impact: Cross-tenant PII dump (membership, user, association-request data); HIGH  
testability: AUTH_HELPED  
class: AUTH  
asset: nc.ipb.de  
confidence: 55  
reasoning: status.php confirms Nextcloud 34.0.3 installed, not in maintenance; capabilities API confirms provisioning_api (user CRUD), impersonate (admin act-as-user), oauth2 (token-based auth), circles (group sharing) all enabled; WebDAV endpoint returns Sabre DAV error with detailed auth guidance; bruteforce delay=0 in capabilities  
evidence_needed: authenticated OCS request showing user listing or impersonation; or unauth endpoint leaking user data  
verify_steps: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, oauth2 scope; GET https://nc.ipb.de/ocs/v2.php/cloud/activity and GET https://nc.ipb.de/ocs/v2.php/cloud/user with empty credentials — check for unauthenticated data leak  
impact: Full user data access + impersonation of any user — CRITICAL if auth obtained  
testability: HUMAN_ONLY  
class: MISCONFIG  
asset: *.ipb.de (eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de)  
confidence: 60  
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths  
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback  
verify_steps: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content length, status vs wildcard; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only  
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist  
testability: PASSIVE  
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy  
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface  
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here  
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)  
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)  
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling  
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend  
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta  
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with provisioning_api + impersonate is CRITICAL value if auth obtained; gold.ipb.de Jitsi with anonymous guest access; cic.ipb.de custom PHP login; two Plesk panels. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-05 00:33:01 UTC [target] (model nemotron3)
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: status.php confirms Nextcloud 34.0.3 installed, not in maintenance; OCS capabilities API confirms provisioning_api (user CRUD), impersonate (admin act-as-user), oauth2, circles, WebDAV all enabled; bruteforce delay=0; WebDAV returns Sabre DAV 401 with auth guidance
evidence_needed: authenticated OCS request showing user listing or impersonation; or unauthenticated endpoint leaking user data
verify_steps: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" (confirm full enabled app list); GET https://nc.ipb.de/ocs/v2.php/cloud/activity (unauth data leak check); GET https://nc.ipb.de/ocs/v2.php/cloud/user (unauth data leak check); all via curl -k to bypass SSL cert verify
impact: Full user data access + impersonation of any user — CRITICAL if auth obtained
testability: HUMAN_ONLY
class: MISCONFIG
asset: *.ipb.de (eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de, app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content length, status vs wildcard; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only (use curl -k for SSL cert failures)
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[PARKED] Kiosk TOTP validation endpoint logic flaw: confidence 40 (at threshold), requires valid token format knowledge, enumeration at scale may trigger WAF, low impact relative to cross-tenant BOLA
[PARKED] Plesk forgery_protection_token static → CSRF on admin panel: confidence 45 but Plesk panels are public login panels (out-of-scope per program rules); staticity unproven; needs passive confirmation first
[PARKED] CIC custom PHP SQL injection: confidence 35, public login panel out-of-scope per program rules, no evidence of vulnerability
[FINAL] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector (confidence 55) — HUMAN_ONLY but CRITICAL value if auth obtained, well-defined verify steps
[FINAL] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[NEXT] PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, oauth2 scope; GET https://nc.ipb.de/ocs/v2.php/cloud/activity and GET https://nc.ipb.de/ocs/v2.php/cloud/user with empty credentials — check for unauthenticated data leak (use curl -k for SSL cert verify bypass)
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with provisioning_api + impersonate is CRITICAL value if auth obtained; gold.ipb.de Jitsi with anonymous guest access; cic.ipb.de custom PHP login; two Plesk panels. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-05 04:55:55 UTC [target] (model nemotron3)
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: status.php confirms Nextcloud 34.0.3 installed, not in maintenance; OCS capabilities API confirms provisioning_api (user CRUD), impersonate (admin act-as-user), oauth2, circles, WebDAV all enabled; bruteforce delay=0; WebDAV returns Sabre DAV 401 with auth guidance
evidence_needed: authenticated OCS request showing user listing or impersonation; or unauthenticated endpoint leaking user data
verify_steps: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" (confirm full enabled app list); GET https://nc.ipb.de/ocs/v2.php/cloud/activity (unauth data leak check); GET https://nc.ipb.de/ocs/v2.php/cloud/user (unauth data leak check); all via curl -k to bypass SSL cert verify
impact: Full user data access + impersonation of any user — CRITICAL if auth obtained
testability: HUMAN_ONLY
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de, app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content length, status vs wildcard; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only (use curl -k for SSL cert failures)
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[PARKED] Kiosk TOTP validation endpoint logic flaw: confidence 40 (at threshold), requires valid token format knowledge, enumeration at scale may trigger WAF, low impact relative to cross-tenant BOLA
[PARKED] Plesk forgery_protection_token static → CSRF on admin panel: confidence 45 but Plesk panels are public login panels (out-of-scope per program rules); staticity unproven; needs passive confirmation first
[PARKED] CIC custom PHP SQL injection: confidence 35, public login panel out-of-scope per program rules, no evidence of vulnerability
[FINAL] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector (confidence 55) — HUMAN_ONLY but CRITICAL value if auth obtained, well-defined verify steps
[FINAL] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[NEXT] PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, oauth2 scope; GET https://nc.ipb.de/ocs/v2.php/cloud/activity and GET https://nc.ipb.de/ocs/v2.php/cloud/user with empty credentials — check for unauthenticated data leak (use curl -k for SSL cert verify bypass)
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with provisioning_api + impersonate is CRITICAL value if auth obtained; gold.ipb.de Jitsi with anonymous guest access; cic.ipb.de custom PHP login; two Plesk panels. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-05 08:45:37 UTC [target] (model nemotron3)
[CHANGED] nc.ipb.de OCS capabilities endpoint confirmed live unauthenticated — returns Nextcloud 34.0.3 version, bruteforce delay=0, theming config; activity/user endpoints require auth (997)
[CHANGED] nc.ipb.de WebDAV (remote.php/dav/) returns 401 NotAuthenticated with Sabre DAV 4.0+ auth guidance
[PRIO] nc.ipb.de,8.0,attack_surface=8,business_value=10,tech_exposure=9,gate_ease=3,cloud_surface=8,freshness=9
[PRIO] *.ipb.de (wildcard),7.85,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=10,cloud_surface=9,freshness=8
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] gold.ipb.de,6.9,attack_surface=6,business_value=7,tech_exposure=7,gate_ease=8,cloud_surface=6,freshness=8
[PRIO] piwik.ipb.de,5.35,attack_surface=5,business_value=6,tech_exposure=5,gate_ease=4,cloud_surface=5,freshness=7
[PRIO] cic.ipb.de,5.15,attack_surface=5,business_value=6,tech_exposure=5,gate_ease=4,cloud_surface=4,freshness=7
[PRIO] eticket.ipb.de,4.35,attack_surface=4,business_value=5,tech_exposure=4,gate_ease=3,cloud_surface=4,freshness=7
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: OCS capabilities confirms Nextcloud 34.0.3, bruteforce delay=0; status.php leaks full version/install status; WebDAV returns Sabre DAV 401 with auth guidance; provisioning_api (user CRUD), impersonate (admin act-as-user), oauth2, circles, WebDAV all enabled per prior deep scan; OCS API accessible unauthenticated for capabilities
evidence_needed: authenticated OCS request showing user listing or impersonation capability; or credentialed access demonstrating provisioning_api/impersonate abuse
verify_steps: 1) GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" (confirmed: version 34.0.3, bruteforce delay=0); 2) GET https://nc.ipb.de/ocs/v2.php/cloud/activity and /cloud/user (confirmed: 997 not logged in); 3) PROPFIND https://nc.ipb.de/remote.php/dav/ (confirmed: 401 NotAuthenticated); 4) With valid credentials, test OCS provisioning_api user listing and impersonate endpoint
impact: Full user data access + impersonation of any user — CRITICAL if auth obtained
testability: HUMAN_ONLY
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (eticket.ipb.de, nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de, app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths; 5 services already confirmed (Nextcloud, Jitsi, 2x Plesk, CIC)
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content length, status vs wildcard; repeat for nc.ipb.de, gold.ipb.de, piwik.ipb.de, webcam.ipb.de, cic.ipb.de at 1 rps GET/HEAD only (use curl -k for SSL cert failures)
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[PARKED] Kiosk TOTP validation endpoint logic flaw: confidence 40 (at threshold), requires valid token format knowledge, enumeration at scale may trigger WAF, low impact relative to cross-tenant BOLA
[PARKED] Plesk forgery_protection_token static → CSRF on admin panel: confidence 45 but Plesk panels are public login panels (out-of-scope per program rules); staticity unproven; needs passive confirmation first
[PARKED] CIC custom PHP SQL injection: confidence 35, public login panel out-of-scope per program rules, no evidence of vulnerability
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] 2) Wildcard DNS masking hidden attack surface on *.ipb.de subdomains (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[FINAL] 3) Nextcloud OCS provisioning_api + impersonate = privilege escalation vector (confidence 55) — HUMAN_ONLY but CRITICAL value if auth obtained, well-defined verify steps
[NEXT] PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak; OCS capabilities unauth; activity/user require auth; WebDAV 401
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with provisioning_api + impersonate is CRITICAL value if auth obtained; gold.ipb.de Jitsi with anonymous guest access; cic.ipb.de custom PHP login; two Plesk panels. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-05 12:16:55 UTC [target] (model nemotron3)
[NEW] nc.ipb.de OCS capabilities endpoint confirmed live unauthenticated via curl -k — returns Nextcloud 34.0.3, bruteforce.delay=0, app_api 34.0.0; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps (prior KC entry overstated)
[NEW] nc.ipb.de WebDAV (remote.php/dav/) returns 401 NotAuthenticated with Sabre DAV 4.0+ auth guidance (live probe with curl -k)
[CHANGED] nc.ipb.de framework-recon corrected: only app_api 34.0.0 confirmed live via OCS capabilities; provisioning_api/impersonate/oauth2/circles remain unconfirmed by live probe
[CHANGED] probe-results.md shows all nc.ipb.de and eticket.ipb.de probes still failing SSL cert verify (Python urllib), but knowledge base confirms live via curl -k manual validation
[PRIO] nc.ipb.de,8.15,attack_surface=9,business_value=10,tech_exposure=9,gate_ease=3,cloud_surface=8,freshness=10
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] *.ipb.de (wildcard),7.60,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=10,cloud_surface=9,freshness=8
[PRIO] gold.ipb.de,6.90,attack_surface=6,business_value=7,tech_exposure=7,gate_ease=8,cloud_surface=6,freshness=8
[PRIO] piwik.ipb.de,5.35,attack_surface=5,business_value=6,tech_exposure=5,gate_ease=4,cloud_surface=5,freshness=7
[PRIO] cic.ipb.de,5.15,attack_surface=5,business_value=6,tech_exposure=5,gate_ease=4,cloud_surface=4,freshness=7
[PRIO] eticket.ipb.de,4.35,attack_surface=4,business_value=5,tech_exposure=4,gate_ease=3,cloud_surface=4,freshness=7
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de, www.cic.ipb.de, speedtest.ipb.de, de-cix.ipb.de, kinski.ipb.de, hostmaster.ipb.de, track.ipb.de, spam.ipb.de, spam01.ipb.de, spam02.ipb.de, ns6.ipb.de, dns2.ipb.de, mail.ipb.de, moderated.gold.ipb.de, focus.gold.ipb.de, mirror.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths; 5 services already confirmed (Nextcloud, Jitsi, 2x Plesk, CIC)
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[HYP] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector
class: AUTH
asset: nc.ipb.de
confidence: 55
reasoning: OCS capabilities confirms Nextcloud 34.0.3, bruteforce delay=0; status.php leaks full version/install status; WebDAV returns Sabre DAV 401 with auth guidance; provisioning_api (user CRUD), impersonate (admin act-as-user), oauth2, circles, WebDAV all enabled per prior deep scan; OCS API accessible unauthenticated for capabilities
evidence_needed: authenticated OCS request showing user listing or impersonation capability; or credentialed access demonstrating provisioning_api/impersonate abuse
verify_steps: 1) GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" (confirmed: version 34.0.3, bruteforce delay=0); 2) GET https://nc.ipb.de/ocs/v2.php/cloud/activity and /cloud/user (confirmed: 997 not logged in); 3) PROPFIND https://nc.ipb.de/remote.php/dav/ (confirmed: 401 NotAuthenticated); 4) With valid credentials, test OCS provisioning_api user listing and impersonate endpoint
impact: Full user data access + impersonation of any user — CRITICAL if auth obtained
testability: HUMAN_ONLY
[PARKED] Kiosk TOTP validation endpoint logic flaw: confidence 40 (at threshold), requires valid token format knowledge, enumeration at scale may trigger WAF, low impact relative to cross-tenant BOLA
[PARKED] Plesk forgery_protection_token static → CSRF on admin panel: confidence 45 but Plesk panels are public login panels (out-of-scope per program rules); staticity unproven; needs passive confirmation first
[PARKED] CIC custom PHP SQL injection: confidence 35, public login panel out-of-scope per program rules, no evidence of vulnerability
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] 2) Wildcard DNS masking hidden attack surface on *.ipb.de subdomains (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[FINAL] 3) Nextcloud OCS provisioning_api + impersonate = privilege escalation vector (confidence 55) — HUMAN_ONLY but CRITICAL value if auth obtained, well-defined verify steps
[NEXT] PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC entry overstated; status.php version leak; WebDAV 401
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak path
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[LEARN] REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persists, no new hidden service
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with provisioning_api + impersonate is CRITICAL value if auth obtained; gold.ipb.de Jitsi with anonymous guest access; cic.ipb.de custom PHP login; two Plesk panels. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-05 15:21:18 UTC [target] (model nemotron3)
[NEW] nc.ipb.de OCS capabilities endpoint confirmed live unauthenticated via curl -k — returns Nextcloud 34.0.3, bruteforce.delay=0, app_api 34.0.0; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe
[NEW] nc.ipb.de WebDAV (remote.php/dav/) returns 401 NotAuthenticated with Sabre DAV 4.0+ auth guidance (live probe with curl -k)
[CHANGED] nc.ipb.de framework-recon corrected: only app_api 34.0.0 confirmed live via OCS capabilities; provisioning_api/impersonate/oauth2/circles remain unconfirmed by live probe
[CHANGED] probe-results.md shows all nc.ipb.de and eticket.ipb.de probes still failing SSL cert verify (Python urllib), but knowledge base confirms live via curl -k manual validation
[PRIO] nc.ipb.de,8.15,attack_surface=9,business_value=10,tech_exposure=9,gate_ease=3,cloud_surface=8,freshness=10
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] *.ipb.de (wildcard),7.60,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=10,cloud_surface=9,freshness=8
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de, www.cic.ipb.de, speedtest.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths; 5 services already confirmed (Nextcloud, Jitsi, 2x Plesk, CIC)
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[HYP] NC AppAPI ExApp auth mis-scoping
class: AUTH
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (+ app_api ExApp registry endpoints)
confidence: 40
reasoning: unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (not 401); prior caps confirmed NC 34.0.3, app_api 34.0.0 active, bruteforce.delay=0; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered
evidence_needed: any valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed
verify_steps: GET /ocs/v2.php/cloud/capabilities (200, done); with token GET /ocs/v2.php/apps/app_api/apps/list; unauth GET each returned ExApp route
impact: external app auth/SSRF mis-scoping — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] Nextcloud OCS provisioning_api + impersonate = privilege escalation vector: provisioning_api/impersonate/oauth2/circles NOT confirmed by live OCS capabilities probe; only app_api 34.0.0 confirmed; prior KC entry overstated; HUMAN_ONLY testability with no confirmed attack surface
[PARKED] Kiosk TOTP validation endpoint logic flaw: confidence 40 (at threshold), requires valid token format knowledge, enumeration at scale may trigger WAF, low impact relative to cross-tenant BOLA
[PARKED] Plesk forgery_protection_token static → CSRF on admin panel: confidence 45 but Plesk panels are public login panels (out-of-scope per program rules); staticity unproven; needs passive confirmation first
[PARKED] CIC custom PHP SQL injection: confidence 35, public login panel out-of-scope per program rules, no evidence of vulnerability
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] 2) Wildcard DNS masking hidden attack surface on *.ipb.de subdomains (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[FINAL] 3) NC AppAPI ExApp auth mis-scoping (confidence 40) — at threshold, HUMAN_ONLY but well-defined verify steps, HIGH impact if session obtained
[NEXT] PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC entry overstated; status.php version leak; WebDAV 401
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak path
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[LEARN] REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persists, no new hidden service
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with app_api 34.0.0 confirmed (bruteforce.delay=0); provisioning_api/impersonate remain unconfirmed by live probe. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-05 17:25:47 UTC [target] (model nemotron3)
[NEW] nc.ipb.de OCS capabilities endpoint confirmed live unauthenticated via curl -k — returns Nextcloud 34.0.3, bruteforce.delay=0, app_api 34.0.0 ONLY; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe (corrected from prior overstatement)
[NEW] nc.ipb.de WebDAV (remote.php/dav/) returns 401 NotAuthenticated with Sabre DAV 4.0+ auth guidance (live probe with curl -k)
[CHANGED] nc.ipb.de framework-recon corrected: only app_api 34.0.0 confirmed live via OCS capabilities; provisioning_api/impersonate/oauth2/circles remain unconfirmed by live probe
[CHANGED] probe-results.md shows all nc.ipb.de and eticket.ipb.de probes still failing SSL cert verify (Python urllib), but knowledge base confirms live via curl -k manual validation
[NEW] guest.gold.ipb.de does not resolve (000) this cycle — Jitsi anonymousdomain config-only, not a separate live vhost
[NEW] *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de all DNS-dead (000) — wildcard mask persists, no new hidden service
[PRIO] nc.ipb.de,8.15,attack_surface=9,business_value=10,tech_exposure=9,gate_ease=3,cloud_surface=8,freshness=10
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] *.ipb.de (wildcard),7.60,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=10,cloud_surface=9,freshness=8
[PRIO] gold.ipb.de,5.85,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cic.ipb.de,4.20,attack_surface=4,business_value=5,tech_exposure=4,gate_ease=10,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de, www.cic.ipb.de, speedtest.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths; 5 services already confirmed (Nextcloud, Jitsi, 2x Plesk, CIC)
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[HYP] NC AppAPI ExApp auth mis-scoping
class: AUTH
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (+ app_api ExApp registry endpoints)
confidence: 40
reasoning: unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (not 401); prior caps confirmed NC 34.0.3, app_api 34.0.0 active, bruteforce.delay=0; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered
evidence_needed: any valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed
verify_steps: GET /ocs/v2.php/cloud/capabilities (200, done); with token GET /ocs/v2.php/apps/app_api/apps/list; unauth GET each returned ExApp route
impact: external app auth/SSRF mis-scoping — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp auth mis-scoping: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session which has no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Kiosk TOTP validation endpoint logic flaw: confidence 40 (at threshold), requires valid token format knowledge, enumeration at scale may trigger WAF, low impact relative to cross-tenant BOLA
[PARKED] Plesk forgery_protection_token static → CSRF on admin panel: confidence 45 but Plesk panels are public login panels (out-of-scope per program rules); staticity unproven; needs passive confirmation first
[PARKED] CIC custom PHP SQL injection: confidence 35, public login panel out-of-scope per program rules, no evidence of vulnerability
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] 2) Wildcard DNS masking hidden attack surface on *.ipb.de subdomains (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[NEXT] PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC entry overstated; status.php version leak; WebDAV 401
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak path
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
[LEARN] REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persists, no new hidden service
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with app_api 34.0.0 confirmed (bruteforce.delay=0); provisioning_api/impersonate remain unconfirmed by live probe. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-05 19:29:29 UTC [target] (model nemotron3)
[CHANGED] nc.ipb.de framework-recon corrected: only app_api 34.0.0 confirmed live via OCS capabilities (curl -k); provisioning_api/impersonate/oauth2/circles remain unconfirmed by live probe
[CHANGED] probe-results.md: all nc.ipb.de/eticket.ipb.de probes fail SSL cert verify (Python urllib), but knowledge base confirms live via curl -k manual validation
[NEW] guest.gold.ipb.de does not resolve (000) — Jitsi anonymousdomain config-only, not a separate live vhost
[NEW] *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de all DNS-dead (000) — wildcard mask persists, no new hidden service
[NEW] cloud.ipb.de resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[NEW] app/auth.gold/my/prod/survey/www.cic.ipb.de: no DNS this cycle — Host-header wildcard-probe approach non-executable; wildcard mask does not resolve these names
[NEW] mirror/spam/spam01/spam02/ns6/mail/speedtest.ipb.de: resolve to distinct IPs but non-web infra (mail/spam/NS) or public apt mirror (mirror = out-of-scope public files)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.30,attack_surface=8,business_value=8,tech_exposure=7,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] *.ipb.de (wildcard),7.60,attack_surface=7,business_value=8,tech_exposure=6,gate_ease=10,cloud_surface=9,freshness=8
[PRIO] gold.ipb.de,5.85,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cic.ipb.de,4.20,attack_surface=4,business_value=5,tech_exposure=4,gate_ease=10,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] Wildcard DNS masking hidden attack surface on *.ipb.de subdomains with live TLS endpoints
class: MISCONFIG
asset: *.ipb.de (app.ipb.de, auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de)
confidence: 60
reasoning: Dedicated deep scan confirms wildcard DNS — 0 genuinely dedicated hosts after shared-IP filtering; 21 inventory hosts never individually re-confirmed post-discovery; SSL cert verify failures on 6 subdomains confirm live TLS endpoints behind wildcard proxy; real services may exist behind wildcard responding only to correct Host headers or specific paths; 5 services already confirmed (Nextcloud, Jitsi, 2x Plesk, CIC)
evidence_needed: live HTTP response on any wildcard subdomain with distinct content vs wildcard fallback
verify_steps: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de
impact: Hidden attack surface (ticketing, Nextcloud, monitoring, webcam, customer portal, auth, cloud, prod API) — HIGH if real services exist
testability: PASSIVE
[HYP] NC AppAPI ExApp auth mis-scoping
class: AUTH
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (+ app_api ExApp registry endpoints)
confidence: 40
reasoning: unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (not 401); prior caps confirmed NC 34.0.3, app_api 34.0.0 active, bruteforce.delay=0; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered
evidence_needed: any valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed
verify_steps: GET /ocs/v2.php/cloud/capabilities (200, done); with token GET /ocs/v2.php/apps/app_api/apps/list; unauth GET each returned ExApp route
impact: external app auth/SSRF mis-scoping — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp auth mis-scoping: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session which has no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[FINAL] 2) Wildcard DNS masking hidden attack surface on *.ipb.de subdomains (confidence 60) — surviving passive hypothesis, testable now with Host header probes, HIGH impact if confirmed, PASSIVE testability
[NEXT] PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.ipb.de, my.ipb.de, prod.ipb.de, survey.ipb.de
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints) all seq-ID auth-gated; top priority pending credentialed access
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never individually re-confirmed; SSL cert failures on 6 subdomains confirm live TLS behind proxy
[LEARN] ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA fallback)
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not re-probe as unauth surface
[LEARN] REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
[LEARN] REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
[LEARN] REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Panel x2 (piwik/webcam), CIC PHP login (cic)
[LEARN] ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
[LEARN] REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential hypothesis killed
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC entry overstated; status.php version leak; WebDAV 401
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak path
[LEARN] ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta; public login panel = out-of-scope class
[LEARN] REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persists, no new hidden service
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same panel as piwik/webcam); login-only out-of-scope class
[RISK] ipb: 68 — 5 new live services behind wildcard proxy dramatically expand attack surface; nc.ipb.de Nextcloud with app_api 34.0.0 confirmed (bruteforce.delay=0); provisioning_api/impersonate remain unconfirmed by live probe. pluto.portal.ipb.de remains top BOLA target (65 confidence). _exceptions self-reg hypothesis killed (no credential path). Validated bugs: 0.
## 2026-09-05 21:41:20 UTC [target] (model nemotron3)
[NEW] guest.gold.ipb.de does not resolve (000) — Jitsi anonymousdomain config-only, not a separate live vhost
[NEW] *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de all DNS-dead (000) — wildcard mask persists, no new hidden service
[NEW] cloud.ipb.de resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[NEW] app/auth.gold/my/prod/survey/www.cic.ipb.de: no DNS this cycle — Host-header wildcard-probe approach non-executable; wildcard mask does not resolve these names
[NEW] mirror/spam/spam01/spam02/ns6/mail/speedtest.ipb.de: resolve to distinct IPs but non-web infra (mail/spam/NS) or public apt mirror (mirror = out-of-scope public files)
[CHANGED] probe-results.md: all nc.ipb.de/eticket.ipb.de probes fail SSL cert verify (Python urllib), but knowledge base confirms live via curl -k manual validation
[CHANGED] nc.ipb.de framework-recon corrected: only app_api 34.0.0 confirmed live via OCS capabilities; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe
[CHANGED] REJECTED MISCONFIG @ *.ipb.de wildcard DNS masking: app/auth.gold/my/prod/survey all DNS-dead; cloud.ipb.de resolves to shared Plesk (out-of-scope); mirror/spam/NS/mail out-of-scope; wildcard mask no longer hides undiscovered attack surface — hypothesis killed
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.30,attack_surface=8,business_value=8,tech_exposure=7,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.85,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cic.ipb.de,4.20,attack_surface=4,business_value=5,tech_exposure=4,gate_ease=10,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.90,attack_surface=3,business_value=4,tech_exposure=3,gate_ease=10,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth
confidence: 40
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable
testability: HUMAN_ONLY
[HYP] Jitsi Meet config.js information disclosure and anonymous guest enumeration
class: MISCONFIG
asset: gold.ipb.de /config.js, guest.gold.ipb.de (if resolved)
confidence: 30
reasoning: Jitsi Meet config.js publicly accessible; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000) this cycle; unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no room-URL leak path confirmed
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time; 4) If guest resolves, probe anonymous join flow for room enumeration
impact: Information disclosure (internal config, feature flags); LOW-MEDIUM; anonymous guest abuse if room enumeration possible
testability: PASSIVE
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Jitsi Meet config.js information disclosure and anonymous guest enumeration: confidence 30 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] PROBE: GET https://pluto.portal.ipb.de/api/system/ -H "Accept: application/json" -k — confirm DRF endpoint responds 401 with WWW-Authenticate: Token (validates auth gate consistency before credentialed BOLA testing)
[LEARN] REJECTED MISCONFIG @ *.ipb.de wildcard DNS masking: app/auth.gold/my/prod/survey all DNS-dead; cloud.ipb.de resolves to shared Plesk (out-of-scope); mirror/spam/NS/mail out-of-scope; wildcard mask no longer hides undiscovered attack surface — hypothesis killed
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same panel as piwik/webcam); login-only out-of-scope class
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-05 23:16:34 UTC [target] (model nemotron3)
[NEW] pluto.portal.ipb.de/api/system/ → HTTP 401 (live DRF endpoint confirmed, WWW-Authenticate: Token)  
[NEW] gold.ipb.de/config.js → SSL cert verify failed (Python urllib), but KB confirms live via curl -k  
[CHANGED] Wildcard DNS masking hypothesis KILLED — 33/33 inventory names fully classified (Plesk panels, DNS-dead set, non-web infra, Jitsi internal components)  
[CHANGED] nc.ipb.de framework-recon corrected: only app_api 34.0.0 confirmed live via OCS capabilities; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe  
[CHANGED] guest.gold.ipb.de does not resolve (000) — Jitsi anonymousdomain config-only, not a live vhost  
[CHANGED] cloud.ipb.de resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope  
[CHANGED] focus.gold.ipb.de + moderated.gold.ipb.de DNS-dead (000) — Jitsi internal components, not external vhosts  
[CHANGED] *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de all DNS-dead (000)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8  
[PRIO] nc.ipb.de,7.30,attack_surface=8,business_value=8,tech_exposure=7,gate_ease=5,cloud_surface=7,freshness=9  
[PRIO] gold.ipb.de,5.85,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=8,cloud_surface=6,freshness=7  
[PRIO] cic.ipb.de,4.20,attack_surface=4,business_value=5,tech_exposure=4,gate_ease=10,cloud_surface=3,freshness=6  
[PRIO] piwik.ipb.de,3.90,attack_surface=3,business_value=4,tech_exposure=3,gate_ease=10,cloud_surface=3,freshness=6  
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API  
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)  
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/  
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH  
testability: AUTH_HELPED  
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps  
class: SSRF  
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth  
confidence: 40  
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps  
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass  
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible  
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable  
testability: HUMAN_ONLY  
[HYP] Jitsi Meet config.js information disclosure and anonymous guest enumeration  
class: MISCONFIG  
asset: gold.ipb.de /config.js, guest.gold.ipb.de (if resolved)  
confidence: 30  
reasoning: Jitsi Meet config.js publicly accessible; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000) this cycle; unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no room-URL leak path confirmed  
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow  
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time; 4) If guest resolves, probe anonymous join flow for room enumeration  
impact: Information disclosure (internal config, feature flags); LOW-MEDIUM; anonymous guest abuse if room enumeration possible  
testability: PASSIVE  
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven  
[PARKED] Jitsi Meet config.js information disclosure and anonymous guest enumeration: confidence 30 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path  
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] PROBE: GET https://pluto.portal.ipb.de/api/system/ -H "Accept: application/json" -k — confirm DRF endpoint responds 401 with WWW-Authenticate: Token (validates auth gate consistency before credentialed BOLA testing)
[LEARN] REJECTED MISCONFIG @ *.ipb.de wildcard DNS masking: app/auth.gold/my/prod/survey all DNS-dead; cloud.ipb.de resolves to shared Plesk (out-of-scope); mirror/spam/NS/mail out-of-scope; wildcard mask no longer hides undiscovered attack surface — hypothesis killed  
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path  
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost  
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same panel as piwik/webcam); login-only out-of-scope class  
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-06 01:00:33 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.30,attack_surface=8,business_value=8,tech_exposure=7,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.85,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=8,cloud_surface=6,freshness=7
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth
confidence: 40
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable
testability: HUMAN_ONLY
[HYP] Jitsi Meet config.js information disclosure and anonymous guest enumeration
class: MISCONFIG
asset: gold.ipb.de /config.js, guest.gold.ipb.de (if resolved)
confidence: 30
reasoning: Jitsi Meet config.js publicly accessible; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000) this cycle; unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no room-URL leak path confirmed
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time; 4) If guest resolves, probe anonymous join flow for room enumeration
impact: Information disclosure (internal config, feature flags); LOW-MEDIUM; anonymous guest abuse if room enumeration possible
testability: PASSIVE
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Jitsi Meet config.js information disclosure and anonymous guest enumeration: confidence 30 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] PROBE: GET https://pluto.portal.ipb.de/api/system/ -H "Accept: application/json" -k — confirm DRF endpoint responds 401 with WWW-Authenticate: Token (validates auth gate consistency before credentialed BOLA testing)
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same panel as piwik/webcam); login-only out-of-scope class
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-06 05:41:21 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth
confidence: 40
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable
testability: HUMAN_ONLY
[HYP] Jitsi Meet config.js information disclosure
class: MISCONFIG
asset: gold.ipb.de /config.js
confidence: 25
reasoning: Jitsi Meet config.js publicly accessible; contains domain config, feature flags; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000); unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no sensitive API keys or internal endpoints observed in config.js
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time
impact: Information disclosure (internal config, feature flags); LOW
testability: PASSIVE
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Jitsi Meet config.js information disclosure: confidence 25 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) and run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{tenant,user,association-request,check-in}/{id}/
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-06 09:41:51 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth
confidence: 40
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable
testability: HUMAN_ONLY
[HYP] Jitsi Meet config.js information disclosure
class: MISCONFIG
asset: gold.ipb.de /config.js
confidence: 25
reasoning: Jitsi Meet config.js publicly accessible; contains domain config, feature flags; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000); unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no sensitive API keys or internal endpoints observed in config.js
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time
impact: Information disclosure (internal config, feature flags); LOW
testability: PASSIVE
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Jitsi Meet config.js information disclosure: confidence 25 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) and run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{tenant,user,association-request,check-in}/{id}/
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-06 13:16:09 UTC [target] (model nemotron3)
[CHANGED] pluto.portal.ipb.de/api/system/ consistently returns HTTP 401 (WWW-Authenticate: Token) across 5 consecutive probe cycles (2026-09-05 21:41 through 2026-09-06 09:42) — DRF endpoint stability confirmed
[CHANGED] gold.ipb.de/config.js consistently fails SSL cert verify in automated probes but KB confirms live via curl -k — Jitsi config.js accessible
[CHANGED] Wildcard DNS masking hypothesis fully killed — 33/33 inventory names classified (Plesk panels ×3, DNS-dead set ×15, non-web infra ×6, Jitsi internal components ×3, pluto/event/www/eticket/nc/gold/cic live); no surface drift
[CHANGED] nc.ipb.de OCS capabilities confirmed: app_api 34.0.0 ONLY live; provisioning_api/impersonate/oauth2/circles remain unconfirmed by live caps — prior KC overstatement corrected and stable
[CHANGED] cloud.ipb.de resolves to 194.29.230.41 → 3rd Plesk "I/P/B/ Cloudhosting Panel" (same as piwik/webcam); login-only out-of-scope class confirmed
[CHANGED] focus.gold.ipb.de + moderated.gold.ipb.de DNS-dead (000) — Jitsi internal focus/moderated components not external vhosts; gold subdomain set fully closed
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth
confidence: 40
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable
testability: HUMAN_ONLY
[HYP] Jitsi Meet config.js information disclosure
class: MISCONFIG
asset: gold.ipb.de /config.js
confidence: 25
reasoning: Jitsi Meet config.js publicly accessible; contains domain config, feature flags; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000); unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no sensitive API keys or internal endpoints observed in config.js
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time
impact: Information disclosure (internal config, feature flags); LOW
testability: PASSIVE
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Jitsi Meet config.js information disclosure: confidence 25 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[FINAL] 1) Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) and run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{tenant,user,association-request,check-in}/{id}/
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-06 16:29:14 UTC [target] (model nemotron3)
[NEW] None — no new live hosts or endpoints discovered this cycle
[CHANGED] pluto.portal.ipb.de/api/system/ consistently HTTP 401 (WWW-Authenticate: Token) across 5 consecutive probe cycles — DRF endpoint stability confirmed
[CHANGED] nc.ipb.de OCS capabilities confirmed: app_api 34.0.0 ONLY live; requires OCS-APIRequest: true header (without → CSRF check failed JSON); provisioning_api/impersonate/oauth2/circles remain unconfirmed by live caps
[CHANGED] gold.ipb.de/config.js accessible via curl -k but fails SSL verify in automated probes — Jitsi config.js confirmed live
[CHANGED] Wildcard DNS masking hypothesis fully killed — 33/33 inventory names classified (Plesk×3, DNS-dead×15, non-web infra×6, Jitsi internal×3, live services×7)
[CHANGED] cloud.ipb.de resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk (same as piwik/webcam); login-only out-of-scope class confirmed
[CHANGED] focus.gold.ipb.de + moderated.gold.ipb.de DNS-dead (000) — Jitsi internal components not external vhosts; gold subdomain set fully closed
[CHANGED] event.ipb.de pretix REST /api/v1/ + /api/v1/organizers/ → 401@58 (Token-gated) — first public pretix API probe, consistent with mature hardening
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth
confidence: 40
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable
testability: HUMAN_ONLY
[HYP] Jitsi Meet config.js information disclosure
class: MISCONFIG
asset: gold.ipb.de /config.js
confidence: 25
reasoning: Jitsi Meet config.js publicly accessible via curl -k; contains domain config, feature flags; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000); unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no sensitive API keys or internal endpoints observed in config.js
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time
impact: Information disclosure (internal config, feature flags); LOW
testability: PASSIVE
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Jitsi Meet config.js information disclosure: confidence 25 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[FINAL] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) and run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{tenant,user,association-request,check-in}/{id}/
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-06 18:30:11 UTC [target] (model nemotron3)
[NEW] None — no new live hosts or endpoints discovered this cycle (2026-09-06 16:29)
[CHANGED] pluto.portal.ipb.de/api/system/ consistently HTTP 401 (WWW-Authenticate: Token) across 5 consecutive probe cycles — DRF endpoint stability confirmed
[CHANGED] nc.ipb.de OCS capabilities confirmed: app_api 34.0.0 ONLY live; requires OCS-APIRequest: true header (without → CSRF check failed JSON); provisioning_api/impersonate/oauth2/circles remain unconfirmed by live caps
[CHANGED] gold.ipb.de/config.js accessible via curl -k but fails SSL verify in automated probes — Jitsi config.js confirmed live
[CHANGED] Wildcard DNS masking hypothesis fully killed — 33/33 inventory names classified (Plesk×3, DNS-dead×15, non-web infra×6, Jitsi internal×3, live services×7)
[CHANGED] cloud.ipb.de resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk (same as piwik/webcam); login-only out-of-scope class confirmed
[CHANGED] focus.gold.ipb.de + moderated.gold.ipb.de DNS-dead (000) — Jitsi internal components not external vhosts; gold subdomain set fully closed
[CHANGED] event.ipb.de pretix REST /api/v1/ + /api/v1/organizers/ → 401@58 (Token-gated) — first public pretix API probe, consistent with mature hardening
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth
confidence: 40
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable
testability: HUMAN_ONLY
[HYP] Jitsi Meet config.js information disclosure
class: MISCONFIG
asset: gold.ipb.de /config.js
confidence: 25
reasoning: Jitsi Meet config.js publicly accessible via curl -k; contains domain config, feature flags; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000); unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no sensitive API keys or internal endpoints observed in config.js
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time
impact: Information disclosure (internal config, feature flags); LOW
testability: PASSIVE
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Jitsi Meet config.js information disclosure: confidence 25 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[FINAL] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login or admin-provisioned account; run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{tenant,user,association-request,check-in}/{id}/
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-06 20:51:02 UTC [target] (model nemotron3)
[CHANGED] pluto.portal.ipb.de/api/system/ consistently HTTP 401 (WWW-Authenticate: Token) across 5+ consecutive probe cycles — DRF endpoint stability confirmed
[CHANGED] nc.ipb.de OCS capabilities confirmed: app_api 34.0.0 ONLY live; requires OCS-APIRequest: true header (without → CSRF check failed JSON); provisioning_api/impersonate/oauth2/circles remain unconfirmed by live caps
[CHANGED] gold.ipb.de/config.js accessible via curl -k but fails SSL verify in automated probes — Jitsi config.js confirmed live
[CHANGED] Wildcard DNS masking hypothesis fully killed — 33/33 inventory names classified (Plesk×3, DNS-dead×15, non-web infra×6, Jitsi internal×3, live services×7)
[CHANGED] cloud.ipb.de resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk (same as piwik/webcam); login-only out-of-scope class confirmed
[CHANGED] focus.gold.ipb.de + moderated.gold.ipb.de DNS-dead (000) — Jitsi internal components not external vhosts; gold subdomain set fully closed
[CHANGED] event.ipb.de pretix REST /api/v1/ + /api/v1/organizers/ → 401@58 (Token-gated) — first public pretix API probe, consistent with mature hardening
[NEW] None — no new live hosts or endpoints discovered this cycle (2026-09-06)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in data); HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp SSRF/auth-bypass via registered external apps
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list (auth), then each ExApp endpoint unauth
confidence: 40
reasoning: NC 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (routing), /ocs/v2.php/cloud/apps → 401; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps list then probe each ExApp endpoint unauth vs authed for SSRF or auth bypass
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with session; 3) For each returned ExApp route, GET unauth and observe status/code; 4) Test SSRF payloads on ExApp endpoints if accessible
impact: SSRF to cloud metadata (169.254.169.254) or internal services; auth bypass on ExApp endpoints; HIGH if exploitable
testability: HUMAN_ONLY
[HYP] Jitsi Meet config.js information disclosure
class: MISCONFIG
asset: gold.ipb.de /config.js
confidence: 25
reasoning: Jitsi Meet config.js publicly accessible via curl -k; contains domain config, feature flags; anonymous guest domain configured (guest.gold.ipb.de) but does not resolve (000); unguessable random roomName rooms by-design; XMPP backend at auth.gold.ipb.de (no live HTTP confirmed); no sensitive API keys or internal endpoints observed in config.js
evidence_needed: distinct sensitive config in config.js (API keys, internal endpoints, feature flags) or resolution of guest.gold.ipb.de exposing anonymous join flow
verify_steps: 1) GET https://gold.ipb.de/config.js -k; 2) Parse for sensitive keys, internal URLs, feature flags; 3) Monitor guest.gold.ipb.de resolution over time
impact: Information disclosure (internal config, feature flags); LOW
testability: PASSIVE
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[PARKED] Jitsi Meet config.js information disclosure: confidence 25 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[FINAL] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — highest confidence, clear verify path with credentials, HIGH impact, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login or admin-provisioned account; run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{tenant,user,association-request,check-in}/{id}/
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-06 22:37:38 UTC [target] (model nemotron3)
[CHANGED] pluto.portal.ipb.de/api/system/ consistently HTTP 401 (WWW-Authenticate: Token) across 6+ consecutive probe cycles — DRF endpoint stability confirmed
[CHANGED] nc.ipb.de OCS capabilities confirmed: app_api 34.0.0 ONLY live; requires OCS-APIRequest: true header (without → CSRF check failed JSON); provisioning_api/impersonate/oauth2/circles remain unconfirmed by live caps
[CHANGED] gold.ipb.de/config.js accessible via curl -k but fails SSL verify in automated probes — Jitsi config.js confirmed live
[CHANGED] event.ipb.de pretix REST /api/v1/ + /api/v1/organizers/ → 401@58 (Token-gated) — first public pretix API probe, consistent with mature hardening
[CHANGED] Wildcard DNS masking hypothesis fully killed — 33/33 inventory names classified (Plesk×3, DNS-dead×15, non-web infra×6, Jitsi internal×3, live services×7)
[CHANGED] cloud.ipb.de resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk (same as piwik/webcam); login-only out-of-scope class confirmed
[CHANGED] focus.gold.ipb.de + moderated.gold.ipb.de DNS-dead (000) — Jitsi internal components not external vhosts; gold subdomain set fully closed
[NEW] None — no new live hosts or endpoints discovered this cycle (2026-09-06)
[NEW] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (from bigpickle model, confidence 45) — new hypothesis class on pluto.portal.ipb.de
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface including /api/system/, motd, check-in, association-request, user-token, self endpoints; /api/system/ 401@58 byte-stable 6+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON) this cycle; classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers
impact: Staff/victim session theft via inline render — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry auth mis-scoping (session-gated, 404 vs 401)
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200@1630 re-confirmed this cycle (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0 unchanged); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] Jitsi Meet config.js information disclosure: confidence 25 (<40), no sensitive config leak confirmed, guest.gold.ipb.de does not resolve (000), unguessable roomName by-design — no viable verify path
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact, credential-gated, HUMAN_ONLY testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login or admin-provisioned account; run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ with tenant-A Token (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-07 00:20:27 UTC [target] (model nemotron3)
[CHANGED] pluto.portal.ipb.de/api/system/ HTTP 401 stable across 7+ consecutive cycles (confirmed live via curl -k)
[CHANGED] nc.ipb.de OCS capabilities 200 with OCS-APIRequest: true header; app_api 34.0.0 only; provisioning_api/impersonate/oauth2/circles unconfirmed by live caps
[CHANGED] gold.ipb.de/config.js 200 via curl -k (SSL cert fails in automated probes)
[CHANGED] event.ipb.de pretix REST /api/v1/ + /api/v1/organizers/ → 401 Token-gated (mature hardening)
[NEW] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion hypothesis (confidence 45) on /api/multi-tenancy/v1/user/profile-picture/{upload,download}/ (both 401 auth-gated)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 7+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/
confidence: 45
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers
impact: Staff/victim session theft via inline render — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry auth mis-scoping (session-gated, 404 vs 401)
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact, credential-gated, HUMAN_ONLY testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login or admin-provisioned account; run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ with tenant-A Token (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-07 04:52:01 UTC [target] (model nemotron3)
[CHANGED] pluto.portal.ipb.de/api/system/ HTTP 401 stable across 7+ consecutive cycles (curl -k confirmed live)
[CHANGED] nc.ipb.de OCS capabilities 200 with OCS-APIRequest: true header; app_api 34.0.0 only; provisioning_api/impersonate/oauth2/circles unconfirmed by live caps
[CHANGED] gold.ipb.de/config.js 200 via curl -k (SSL cert fails in automated probes)
[CHANGED] event.ipb.de pretix REST /api/v1/ + /api/v1/organizers/ → 401 Token-gated (mature hardening)
[NEW] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion hypothesis (confidence 45) on /api/multi-tenancy/v1/user/profile-picture/{upload,download}/ (both 401 auth-gated)
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 7+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/
confidence: 45
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers
impact: Staff/victim session theft via inline render — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry auth mis-scoping (session-gated, 404 vs 401)
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact, credential-gated, HUMAN_ONLY testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login or admin-provisioned account; run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ with tenant-A Token (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-07 09:54:44 UTC [target] (model nemotron3)
[NEW] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion hypothesis (confidence 45) on `/api/multi-tenancy/v1/user/profile-picture/{upload,download}/` — both endpoints confirmed 401 auth-gated DRF endpoints
[CHANGED] pluto.portal.ipb.de `/api/system/` HTTP 401 stable across 7+ consecutive cycles (curl -k confirmed live)
[CHANGED] nc.ipb.de OCS capabilities 200 with `OCS-APIRequest: true` header; app_api 34.0.0 only; provisioning_api/impersonate/oauth2/circles unconfirmed by live caps
[CHANGED] gold.ipb.de `/config.js` 200 via curl -k (SSL cert fails in automated probes)
[CHANGED] event.ipb.de pretix REST `/api/v1/` + `/api/v1/organizers/` → 401 Token-gated (mature hardening)
[CHANGED] Wildcard DNS masking hypothesis fully closed — 33/33 inventory names classified (Plesk×3, DNS-dead×15, non-web infra×6, Jitsi internal×3, live services×7); no surface drift
[PRIO] pluto.portal.ipb.de,7.65,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] nc.ipb.de,7.10,attack_surface=8,business_value=8,tech_exposure=6,gate_ease=5,cloud_surface=7,freshness=9
[PRIO] gold.ipb.de,5.60,attack_surface=6,business_value=6,tech_exposure=4,gate_ease=8,cloud_surface=6,freshness=7
[PRIO] cloud.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] cic.ipb.de,3.20,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=7,cloud_surface=3,freshness=6
[PRIO] piwik.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[PRIO] webcam.ipb.de,3.10,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=8,cloud_surface=3,freshness=6
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 7+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/
confidence: 45
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers
impact: Staff/victim session theft via inline render — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry auth mis-scoping (session-gated, 404 vs 401)
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp SSRF/auth-bypass via registered external apps: confidence 40 (at threshold), HUMAN_ONLY testability, requires valid NC session with no confirmed acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live probe — attack surface unproven
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact, credential-gated, HUMAN_ONLY testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login or admin-provisioned account; run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ with tenant-A Token (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-07 15:30:41 UTC [target] (model nemotron3)
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 8+ cycles  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)  
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/  
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL  
testability: AUTH_HELPED
class: XSS  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/  
confidence: 45  
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)  
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)  
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers  
impact: Staff/victim session theft via inline render — HIGH, credential-gated  
testability: HUMAN_ONLY
class: SSRF  
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list  
confidence: 40  
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps  
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed  
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints  
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained  
testability: HUMAN_ONLY
## 2026-09-07 19:23:46 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.0,attack_surface=8 business_value=9 tech_exposure=6 gate_ease=2 cloud_surface=3 freshness=2
[PRIO] nc.ipb.de,5.0,attack_surface=6 business_value=6 tech_exposure=5 gate_ease=3 cloud_surface=5 freshness=3
[PRIO] gold.ipb.de,3.9,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=3
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 8+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] Jitsi Meet anonymous guest domain configuration-only, no live vhost
class: MISCONFIG
asset: guest.gold.ipb.de
confidence: 35
reasoning: guest.gold.ipb.de does not resolve (000) this cycle and prior; Jitsi config.js shows anonymousdomain: 'guest.gold.ipb.de' but no separate vhost provisioned; focus.gold.ipb.de and moderated.gold.ipb.de also DNS-dead (internal components only)
evidence_needed: DNS resolution proof or live HTTP on guest.gold.ipb.de
verify_steps: 1) DNS query guest.gold.ipb.de (A/AAAA/CNAME); 2) HTTPS GET https://guest.gold.ipb.de/ (expect 000/timeout); 3) Confirm Jitsi config.js anonymousdomain is config-only artifact
impact: None — configuration artifact, not attack surface
testability: PASSIVE
[PARKED] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY testability with no confirmed NC session acquisition path; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — attack surface unproven
[PARKED] Jitsi Meet anonymous guest domain configuration-only: confidence 35 < 40 — drop
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact, credential-gated, HUMAN_ONLY testability (from prior leads, same asset as #1)
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 8+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: cycle converged without new data — all live surfaces byte-stable, 33/33 inventory classified; no new unauth vector; earlier "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-07 22:15:44 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,7.1,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=2  
[PRIO] nc.ipb.de,5.2,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=2  
[PRIO] gold.ipb.de,3.8,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=2  
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API  
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 9+ cycles  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)  
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/  
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL  
testability: AUTH_HELPED  
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion  
class: XSS  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/  
confidence: 45  
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)  
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)  
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers  
impact: Staff/victim session theft via inline render — HIGH, credential-gated  
testability: HUMAN_ONLY  
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF  
class: SSRF  
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list  
confidence: 40  
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps  
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed  
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints  
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained  
testability: HUMAN_ONLY  
[PARKED] Jitsi Meet anonymous guest domain configuration-only: confidence 35 < 40 — drop  
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability  
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact, credential-gated, HUMAN_ONLY testability (same asset as #1)  
[FINAL] 3. Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF (confidence 40) — HIGH impact if session obtained, HUMAN_ONLY testability, no confirmed NC session acquisition path  
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain  
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 9+ consecutive stable cycles  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path  
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost  
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift  
[LEARN] ACCEPTED MISC @ *.ipb.de: cycle converged without new data — all live surfaces byte-stable, 33/33 inventory classified; no new unauth vector; earlier "do not re-probe" constraints honored  
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-08 00:32:49 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,7.1,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=2  
[PRIO] nc.ipb.de,5.2,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=2  
[PRIO] gold.ipb.de,3.8,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=2  
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API  
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/multi-tenancy/v1/membership/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 9+ cycles  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)  
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/  
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL  
testability: AUTH_HELPED  
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion  
class: XSS  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/  
confidence: 45  
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)  
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)  
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers  
impact: Staff/victim session theft via inline render — HIGH, credential-gated  
testability: HUMAN_ONLY  
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF  
class: SSRF  
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list  
confidence: 40  
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps  
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed  
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints  
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained  
testability: HUMAN_ONLY  
[PARKED] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold, but no NC session acquisition path confirmed (HUMAN_ONLY blocker); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — attack surface unvalidated without session  
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability  
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact, credential-gated, HUMAN_ONLY testability (same asset as #1)  
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 9+ consecutive stable cycles  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged  
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost  
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift  
[LEARN] ACCEPTED MISC @ *.ipb.de: cycle converged without new data — all live surfaces byte-stable, 33/33 inventory classified; no new unauth vector; earlier "do not re-probe" constraints honored  
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-08 05:17:59 UTC [target] (model nemotron3)
[CHANGED] pluto.portal.ipb.de/api/system/ → HTTP 401 stable across 10+ consecutive cycles (live re-confirmed this cycle)  
[CHANGED] nc.ipb.de OCS capabilities → HTTP 200 with OCS-APIRequest: true header; app_api 34.0.0 only; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — unchanged  
[CHANGED] gold.ipb.de/config.js → HTTP 200 via curl -k (SSL cert fails in automated probes) — unchanged  
[CHANGED] event.ipb.de pretix REST /api/v1/ + /api/v1/organizers/ → 401 Token-gated — mature hardening, saturated  
[CHANGED] *.ipb.de wildcard DNS masking → 10th consecutive converged cycle; passive DNS stable (pluto/gold/cloud unchanged, my/prod/app/guest/focus/survey DNS-dead), zero surface drift; vrle.de (49.13.50.5, Hetzner) resolves but outside ipb.de scope — excluded  
[PRIO] pluto.portal.ipb.de,7.1,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=2  
[PRIO] nc.ipb.de,5.2,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=2  
[PRIO] gold.ipb.de,3.8,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=2  
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API  
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/multi-tenancy/v1/membership/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 10+ cycles  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)  
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/  
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL  
testability: AUTH_HELPED  
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion  
class: XSS  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/  
confidence: 45  
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)  
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)  
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers  
impact: Staff/victim session theft via inline render — HIGH, credential-gated  
testability: HUMAN_ONLY  
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF  
class: SSRF  
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list  
confidence: 40  
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps  
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed  
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints  
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained  
testability: HUMAN_ONLY  
[PARKED] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion: confidence 45 at threshold, but HUMAN_ONLY blocker — no credentialed upload/download path without tenant Token; same asset as #1, requires HUMAN action first  
[PARKED] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold, but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session  
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability  
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain  
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 10+ consecutive stable cycles  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged  
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost  
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift  
[LEARN] ACCEPTED MISC @ *.ipb.de: 10th consecutive converged cycle — passive DNS re-confirmed stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored  
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-08 09:56:32 UTC [target] (model nemotron3)
[CHANGED] pluto.portal.ipb.de/api/system/ → HTTP 401 stable across 10+ consecutive cycles (live re-confirmed this cycle)
[CHANGED] nc.ipb.de OCS capabilities → HTTP 200 with OCS-APIRequest: true header; app_api 34.0.0 only; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — unchanged
[CHANGED] gold.ipb.de/config.js → HTTP 200 via curl -k (SSL cert fails in automated probes) — unchanged
[CHANGED] event.ipb.de pretix REST /api/v1/ + /api/v1/organizers/ → 401 Token-gated — mature hardening, saturated
[CHANGED] *.ipb.de wildcard DNS masking → 10th consecutive converged cycle; passive DNS stable (pluto/gold/cloud unchanged, my/prod/app/guest/focus/survey DNS-dead), zero surface drift; vrle.de (49.13.50.5, Hetzner) resolves but outside ipb.de scope — excluded
[PRIO] pluto.portal.ipb.de,7.1,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=2
[PRIO] nc.ipb.de,5.2,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=2
[PRIO] gold.ipb.de,3.8,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=2
[PRIO] cloud.ipb.de,2.1,attack_surface=2 business_value=2 tech_exposure=2 gate_ease=8 cloud_surface=2 freshness=1
[PRIO] cic.ipb.de,1.5,attack_surface=2 business_value=2 tech_exposure=1 gate_ease=8 cloud_surface=1 freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/multi-tenancy/v1/membership/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 10+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/
confidence: 45
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers
impact: Staff/victim session theft via inline render — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion: confidence 45 at threshold, but HUMAN_ONLY blocker — no credentialed upload/download path without tenant Token; same asset as #1, requires HUMAN action first
[PARKED] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold, but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 10+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 10th consecutive converged cycle — passive DNS re-confirmed stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-08 14:06:14 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,7.1,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=1
[PRIO] nc.ipb.de,5.2,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=1
[PRIO] gold.ipb.de,3.8,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=1
[PRIO] cloud.ipb.de,2.1,attack_surface=2 business_value=2 tech_exposure=2 gate_ease=8 cloud_surface=2 freshness=1
[PRIO] cic.ipb.de,1.5,attack_surface=2 business_value=2 tech_exposure=1 gate_ease=8 cloud_surface=1 freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/tenant/{id}/, /api/multi-tenancy/v1/user/{id}/, /api/multi-tenancy/v1/association-request/{id}/, /api/check-in/v1/check-in/{id}/, /api/multi-tenancy/v1/membership/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 10+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/
confidence: 45
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers
impact: Staff/victim session theft via inline render — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion: confidence 45 at threshold, but HUMAN_ONLY blocker — no credentialed upload/download path without tenant Token; same asset as #1, requires HUMAN action first
[PARKED] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold, but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 10+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 10th consecutive converged cycle — passive DNS re-confirmed stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-08 17:47:50 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,7.1,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=1
[PRIO] nc.ipb.de,5.2,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=1
[PRIO] gold.ipb.de,3.8,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 11+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/
confidence: 45
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers
impact: Staff/victim session theft via inline render — HIGH, credential-gated
testability: HUMAN_ONLY
[PARKED] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold, but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session
[PARKED] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion: confidence 45 at threshold, but HUMAN_ONLY blocker — no credentialed upload/download path without tenant Token; same asset as #1, requires HUMAN action first
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 11+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 10th consecutive converged cycle — passive DNS re-confirmed stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-08 20:31:47 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,7.1,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=1  
[PRIO] nc.ipb.de,5.2,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=1  
[PRIO] gold.ipb.de,3.8,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=1  
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 11+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/upload/, /api/multi-tenancy/v1/user/profile-picture/download/
confidence: 45
reasoning: upload + download endpoints live-confirmed DRF (401 Token-auth, JSON); classic inline-render XSS chain if attacker-controlled SVG bytes served with inline Content-Type; doubly credential-gated (upload + victim session)
evidence_needed: tenant Token; POST SVG (image/svg+xml or text/html) → GET /download/ record Content-Type + Content-Disposition (inline vs attachment)
verify_steps: 1) Obtain tenant Token; 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with SVG payload (Content-Type: image/svg+xml); 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ and inspect Content-Type + Content-Disposition headers
impact: Staff/victim session theft via inline render — HIGH, credential-gated
testability: HUMAN_ONLY
[PARKED] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold, but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session  
[PARKED] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion: confidence 45 at threshold, but HUMAN_ONLY blocker — no credentialed upload/download path without tenant Token; same asset as #1, requires HUMAN action first  
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 11+ consecutive stable cycles  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged  
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost  
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift  
[LEARN] ACCEPTED MISC @ *.ipb.de: 10th consecutive converged cycle — passive DNS re-confirmed stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored  
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-08 23:00:42 UTC [target] (model nemotron3)
## 2026-09-09 00:59:34 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.3,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=1
[PRIO] nc.ipb.de,5.0,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=1
[PRIO] gold.ipb.de,3.7,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 11+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] Jitsi Meet anonymous guest domain config-only — no exploitable surface
class: MISCONFIG
asset: gold.ipb.de /config.js, guest.gold.ipb.de
confidence: 25
reasoning: config.js public but only reveals unguessable random roomName generation; anonymous guest domain (guest.gold.ipb.de) is config-only (DNS 000), not a live vhost; focus.gold.ipb.de/moderated.gold.ipb.de DNS-dead — internal Jitsi components; no room-URL leak path, no unauth meeting enumeration
evidence_needed: proof of meeting metadata leak or guest domain resolution
verify_steps: 1) GET /config.js confirm roomName entropy; 2) DNS query guest.gold.ipb.de → 000; 3) Probe focus.gold.ipb.de/moderated.gold.ipb.de → DNS-dead
impact: None exploitable — configuration hardening confirmed
testability: PASSIVE
[PARKED] Nextcloud AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session
[PARKED] Jitsi Meet anonymous guest domain config-only: confidence 25 < 40 — no exploitable surface, configuration-only finding
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 11+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 10th consecutive converged cycle — passive DNS re-confirmed stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-09 05:45:57 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.3,attack_surface=9 business_value=9 tech_exposure=7 gate_ease=2 cloud_surface=3 freshness=1
[PRIO] nc.ipb.de,5.0,attack_surface=6 business_value=6 tech_exposure=6 gate_ease=3 cloud_surface=5 freshness=1
[PRIO] gold.ipb.de,3.7,attack_surface=4 business_value=4 tech_exposure=3 gate_ease=6 cloud_surface=2 freshness=1
[PRIO] cic.ipb.de,2.5,attack_surface=3 business_value=3 tech_exposure=4 gate_ease=4 cloud_surface=1 freshness=1
[PRIO] cloud.ipb.de,2.0,attack_surface=2 business_value=3 tech_exposure=2 gate_ease=5 cloud_surface=2 freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 13+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] Profile-picture upload SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: Both endpoints confirmed 401 auth-gated; download serves user-uploaded content; Content-Type trust on upload + reflection on download is the only open chain; unchanged 13+ cycles; SVG upload with image/svg+xml → download serves inline vs attachment determines exploitability
evidence_needed: valid tenant Token; upload SVG with image/svg+xml Content-Type; re-download to observe served Content-Type/body (inline vs attachment)
verify_steps: 1) With tenant Token POST /api/multi-tenancy/v1/user/profile-picture/upload/ Content-Type: image/svg+xml body=<svg onload=alert(1)>; 2) GET /api/multi-tenancy/v1/user/profile-picture/download/ Accept: image/svg+xml; 3) Observe Content-Disposition and Content-Type in response
impact: Stored XSS in user profile context — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] Profile-picture upload SVG stored-XSS via content-type confidence: confidence 45 at threshold but HUMAN_ONLY blocker — no credentialed access path for pluto; same token acquisition dependency as BOLA lead
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 13+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 13th consecutive converged cycle — passive DNS re-confirmed stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-09 10:10:57 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.30,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1  
[PRIO] nc.ipb.de,5.00,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1  
[PRIO] gold.ipb.de,3.70,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1  
[PRIO] cic.ipb.de,2.50,attack_surface=3,business_value=3,tech_exposure=4,gate_ease=4,cloud_surface=1,freshness=1  
[PRIO] cloud.ipb.de,2.00,attack_surface=2,business_value=3,tech_exposure=2,gate_ease=5,cloud_surface=2,freshness=1  
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API  
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 14+ cycles  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)  
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/  
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL  
testability: AUTH_HELPED  
[HYP] Profile-picture upload SVG stored-XSS via content-type confusion  
class: XSS  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/  
confidence: 45  
reasoning: Both endpoints confirmed 401 auth-gated; download serves user-uploaded content; Content-Type trust on upload + reflection on download is the only open chain; unchanged 14+ cycles; SVG upload with image/svg+xml → download serves inline vs attachment determines exploitability  
evidence_needed: valid tenant Token; upload SVG with image/svg+xml Content-Type; re-download to observe served Content-Type/body (inline vs attachment)  
verify_steps: 1) With tenant Token POST /api/multi-tenancy/v1/user/profile-picture/upload/ Content-Type: image/svg+xml body=<svg onload=alert(1)>; 2) GET /api/multi-tenancy/v1/user/profile-picture/download/ Accept: image/svg+xml; 3) Observe Content-Disposition and Content-Type in response  
impact: Stored XSS in user profile context — HIGH, credential-gated  
testability: HUMAN_ONLY  
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF  
class: SSRF  
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list  
confidence: 40  
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps  
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed  
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints  
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained  
testability: HUMAN_ONLY  
[PARKED] Profile-picture upload SVG stored-XSS via content-type confusion: confidence 45 at threshold but HUMAN_ONLY blocker — no credentialed access path for pluto; same token acquisition dependency as BOLA lead  
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session  
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability  
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain  
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 14+ consecutive stable cycles  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged  
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost  
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift  
[LEARN] ACCEPTED MISC @ *.ipb.de: 14th consecutive converged cycle — passive DNS re-confirmed stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored  
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-09 14:44:21 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.30,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,5.00,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.70,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[PRIO] cic.ipb.de,2.50,attack_surface=3,business_value=3,tech_exposure=4,gate_ease=4,cloud_surface=1,freshness=1
[PRIO] cloud.ipb.de,2.00,attack_surface=2,business_value=3,tech_exposure=2,gate_ease=5,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 15+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] Profile-picture upload SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: Both endpoints confirmed 401 auth-gated; download serves user-uploaded content; Content-Type trust on upload + reflection on download is the only open chain; unchanged 15+ cycles; SVG upload with image/svg+xml → download serves inline vs attachment determines exploitability
evidence_needed: valid tenant Token; upload SVG with image/svg+xml Content-Type; re-download to observe served Content-Type/body (inline vs attachment)
verify_steps: 1) With tenant Token POST /api/multi-tenancy/v1/user/profile-picture/upload/ Content-Type: image/svg+xml body=<svg onload=alert(1)>; 2) GET /api/multi-tenancy/v1/user/profile-picture/download/ Accept: image/svg+xml; 3) Observe Content-Disposition and Content-Type in response
impact: Stored XSS in user profile context — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] Profile-picture upload SVG stored-XSS via content-type confusion: confidence 45 at threshold but HUMAN_ONLY blocker — no credentialed access path for pluto; same token acquisition dependency as BOLA lead
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 15+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 15th consecutive converged cycle — passive DNS re-confirmed stable map (pluto/nc/gold/cloud/event resolve, my/prod/app/guest.gold/focus/survey DNS-dead), zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-09 18:00:09 UTC [target] (model nemotron3)
[NEW] None — 15+ consecutive converged cycles, zero surface drift across all live endpoints
[CHANGED] pluto.portal.ipb.de/api/system/ → HTTP 401 stable (15+ cycles, byte-stable 354606 SPA fallback)
[CHANGED] nc.ipb.de OCS capabilities → HTTP 200 with OCS-APIRequest: true; app_api 34.0.0 ONLY; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
[CHANGED] gold.ipb.de/config.js → HTTP 200 via curl -k; Jitsi config.js public, anonymous guest by-design, unguessable roomName
[CHANGED] event.ipb.de pretix REST /api/v1/ → 401 Token-gated; mature hardening saturated
[CHANGED] *.ipb.de wildcard DNS masking → 15th consecutive converged cycle; passive DNS stable map (pluto/nc/gold/cloud/event resolve, my/prod/app/guest.gold/focus/survey DNS-dead), zero surface drift
[PRIO] pluto.portal.ipb.de,6.30,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,5.00,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.70,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[PRIO] cic.ipb.de,2.50,attack_surface=3,business_value=3,tech_exposure=4,gate_ease=4,cloud_surface=1,freshness=1
[PRIO] cloud.ipb.de,2.00,attack_surface=2,business_value=3,tech_exposure=2,gate_ease=5,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 15+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] Profile-picture upload SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: Both endpoints confirmed 401 auth-gated; download serves user-uploaded content; Content-Type trust on upload + reflection on download is the only open chain; unchanged 15+ cycles; SVG upload with image/svg+xml → download serves inline vs attachment determines exploitability
evidence_needed: valid tenant Token; upload SVG with image/svg+xml Content-Type; re-download to observe served Content-Type/body (inline vs attachment)
verify_steps: 1) With tenant Token POST /api/multi-tenancy/v1/user/profile-picture/upload/ Content-Type: image/svg+xml body=<svg onload=alert(1)>; 2) GET /api/multi-tenancy/v1/user/profile-picture/download/ Accept: image/svg+xml; 3) Observe Content-Disposition and Content-Type in response
impact: Stored XSS in user profile context — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] Profile-picture upload SVG stored-XSS via content-type confusion: confidence 45 at threshold but HUMAN_ONLY blocker — no credentialed access path for pluto; same token acquisition dependency as BOLA lead
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via kiosk_login flow or admin-provisioned account; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET); same token then confirms profile-picture upload SVG → /download/ Content-Type inline-vs-attachment for the 45 XSS chain
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 15+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 15th consecutive converged cycle — passive DNS re-confirmed stable map (pluto/nc/gold/cloud/event resolve, my/prod/app/guest.gold/focus/survey DNS-dead), zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-09 20:24:42 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.30,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,5.00,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.70,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[PRIO] cic.ipb.de,2.50,attack_surface=3,business_value=3,tech_exposure=4,gate_ease=4,cloud_surface=1,freshness=1
[PRIO] cloud.ipb.de,2.00,attack_surface=2,business_value=3,tech_exposure=2,gate_ease=5,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 15+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] Profile-picture upload SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: Both endpoints confirmed 401 auth-gated; download serves user-uploaded content; Content-Type trust on upload + reflection on download is the only open chain; unchanged 15+ cycles; SVG upload with image/svg+xml → download serves inline vs attachment determines exploitability
evidence_needed: valid tenant Token; upload SVG with image/svg+xml Content-Type; re-download to observe served Content-Type/body (inline vs attachment)
verify_steps: 1) With tenant Token POST /api/multi-tenancy/v1/user/profile-picture/upload/ Content-Type: image/svg+xml body=<svg onload=alert(1)>; 2) GET /api/multi-tenancy/v1/user/profile-picture/download/ Accept: image/svg+xml; 3) Observe Content-Disposition and Content-Type in response
impact: Stored XSS in user profile context — HIGH, credential-gated
testability: HUMAN_ONLY
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] Profile-picture upload SVG stored-XSS via content-type confusion: confidence 45 at threshold but HUMAN_ONLY blocker — no credentialed access path for pluto; same token acquisition dependency as BOLA lead
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker — no NC session acquisition path confirmed; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps; attack surface unvalidated without session
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned account or a clean self-registered kiosk_login credential; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET)
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 15+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 15th consecutive converged cycle — passive DNS re-confirmed stable map (pluto/nc/gold/cloud/event resolve, my/prod/app/guest.gold/focus/survey DNS-dead), zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-09 23:03:15 UTC [target] (model nemotron3)
## 2026-09-10 00:43:36 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.30,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,5.00,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.70,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[PRIO] cic.ipb.de,2.50,attack_surface=3,business_value=3,tech_exposure=4,gate_ease=4,cloud_surface=1,freshness=1
[PRIO] cloud.ipb.de,2.00,attack_surface=2,business_value=3,tech_exposure=2,gate_ease=5,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 16+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] Jitsi config.js internal endpoint disclosure via XMPP backend reference
class: MISCONFIG
asset: gold.ipb.de /config.js
confidence: 30
reasoning: config.js publicly accessible (200 via curl -k); references anonymous guest domain (guest.gold.ipb.de, DNS-dead) and XMPP backend (auth.gold.ipb.de, DNS-dead); Jitsi internal components (focus/moderated) not external vhosts; no room-URL leak path confirmed; config.js may leak internal service references or deployment details
evidence_needed: config.js content showing internal IPs, non-public endpoints, or credential hints
verify_steps: 1) GET https://gold.ipb.de/config.js (curl -k) → parse for internal hosts, API endpoints, secrets; 2) Cross-reference any discovered internal hosts against inventory
impact: Information disclosure aiding internal reconnaissance — LOW
testability: PASSIVE
[PARKED] Jitsi config.js internal endpoint disclosure via XMPP backend reference: confidence 30 < 40 threshold; config.js already public by design, XMPP backend DNS-dead, no verified leak path
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. NC AppAPI ExApp registry auth mis-scoping enabling SSRF (confidence 40) — HIGH impact if session obtained, HUMAN_ONLY blocker, at threshold
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned account or a clean self-registered kiosk_login credential; with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET)
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 16+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[LEARN] ACCEPTED MISC @ *.ipb.de: 16th consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-10 05:29:18 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.30,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,5.00,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.70,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[PRIO] cic.ipb.de,2.50,attack_surface=3,business_value=3,tech_exposure=4,gate_ease=4,cloud_surface=1,freshness=1
[PRIO] cloud.ipb.de,2.00,attack_surface=2,business_value=3,tech_exposure=2,gate_ease=5,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 16+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via kiosk_login flow or admin-provisioned account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[PARKED] Jitsi config.js internal endpoint disclosure via XMPP backend reference: confidence 30 < 40 threshold; config.js already public by design, XMPP backend DNS-dead, no verified leak path
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 17th consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 16+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-10 10:02:05 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.30,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,5.00,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.70,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[PRIO] cic.ipb.de,2.50,attack_surface=3,business_value=3,tech_exposure=4,gate_ease=4,cloud_surface=1,freshness=1
[PRIO] cloud.ipb.de,2.00,attack_surface=2,business_value=3,tech_exposure=2,gate_ease=5,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 17+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 17th consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 17+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-10 14:36:43 UTC [target] (model nemotron3)
[NEW] None — 17th consecutive converged cycle, passive DNS re-confirmed identical stable map (pluto=194.29.225.140, nc=194.29.226.157, gold=194.29.226.155, cloud=194.29.230.41, event/eticket=194.29.230.43, www=194.29.230.44, cic=194.29.226.24, piwik/webcam=194.29.230.41; my/prod/app/guest.gold/focus/survey DNS-dead), zero surface drift
[CHANGED] None — all live endpoints byte-stable: pluto/api/system/ 401@58 (17+ cycles), nc OCS caps 200@1630 (OCS-APIRequest: true, app_api 34.0.0 only), gold/config.js 200 via curl -k, event/api/v1/ 401 Token-gated
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 17+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 17th consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 17+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-10 17:57:52 UTC [target] (model nemotron3)
[NEW] None — 18th consecutive converged cycle, passive DNS re-confirmed identical stable map (pluto=194.29.225.140, nc=194.29.226.157, gold=194.29.226.155, cloud=194.29.230.41, event/eticket=194.29.230.43, www=194.29.230.44, cic=194.29.226.24, piwik/webcam=194.29.230.41; my/prod/app/survey/guest.gold/focus.gold DNS-dead), zero surface drift
[CHANGED] None — all live endpoints byte-stable: pluto/api/system/ 401@58 (17+ cycles), nc OCS caps 200@1630 (OCS-APIRequest: true, app_api 34.0.0 only), gold/config.js 200 via curl -k, event/api/v1/ 401 Token-gated
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 17+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 18th consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 17+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, pluto same IP; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-10 20:14:54 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 18+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 19th consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 18+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-10 22:37:48 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1  
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1  
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API  
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 19+ cycles  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)  
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/  
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL  
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion  
class: XSS  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/  
confidence: 45  
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes  
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render  
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked  
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH  
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF  
class: SSRF  
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list  
confidence: 40  
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps  
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed  
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints  
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained  
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability  
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 20th consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored  
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 19+ consecutive stable cycles  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged  
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost  
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod still DNS-dead, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-11 00:32:58 UTC [target] (model nemotron3)
[CHANGED] pluto.portal.ipb.de/api/system/ → HTTP 401 stable across 20+ consecutive cycles (byte-stable 354606 SPA fallback on schema endpoints)
[CHANGED] nc.ipb.de OCS capabilities → HTTP 200 with OCS-APIRequest: true header; app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — unchanged
[CHANGED] gold.ipb.de/config.js → HTTP 200 via curl -k (SSL cert fails in automated probes) — unchanged
[CHANGED] event.ipb.de pretix REST /api/v1/ → 401 Token-gated; mature hardening saturated
[CHANGED] *.ipb.de wildcard DNS masking → 21st consecutive converged cycle; passive DNS stable map (pluto=194.29.225.140, nc=194.29.226.157, gold=194.29.226.155, cloud=194.29.230.41, event/eticket=194.29.230.43, www=194.29.230.44, cic=194.29.226.24, piwik/webcam=194.29.230.41; my/prod/app/survey/guest.gold/focus.gold DNS-dead), zero surface drift
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 20+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 21st consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 20+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/auth.gold/focus.gold/app/prod/survey still DNS-dead, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-11 05:10:29 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 20+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 22nd consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 21+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold still DNS-dead, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-11 09:50:20 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 20+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 22nd consecutive converged cycle — passive DNS re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 21+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold still DNS-dead, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-11 13:59:09 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 20+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 23rd consecutive converged cycle — passive DNS (getent, no HTTP) re-confirmed identical stable map, zero surface drift; probe-results/triage empty; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 20+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback — do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-11 17:29:40 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 21+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 23rd consecutive converged cycle — passive DNS (getent, no HTTP) re-confirmed identical stable map, zero surface drift; probe-results/triage empty; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 20+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback — do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-11 20:03:36 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 21+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 23rd consecutive converged cycle — passive DNS (getent, no HTTP) re-confirmed identical stable map, zero surface drift; probe-results/triage empty; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 20+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback — do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-11 22:24:14 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 21+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 24th consecutive converged cycle — passive DNS (getent, no HTTP) re-confirmed identical stable map, zero surface drift; probe-results/triage empty; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 21+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback — do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-12 00:23:47 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 21+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 25th consecutive converged cycle — passive DNS (getent, no HTTP) re-confirmed identical stable map (pluto/nc/gold/cloud/event/eticket/www/cic/piwik/webcam resolve, IPv4 unchanged; my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN), zero surface drift; probe-results/triage empty; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 22+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback — do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-12 05:05:44 UTC [target] (model nemotron3)
## 2026-09-12 09:00:42 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 22+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/
impact: Cross-tenant PII dump (membership, user, association-request, check-in, tenant relationship data); CRITICAL
testability: AUTH_HELPED
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 27th consecutive converged cycle — passive DNS (getent, no HTTP) re-confirmed identical stable map, zero surface drift; probe-results/triage empty; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 22+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback — do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-12 12:48:12 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API
class: IDOR
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/
confidence: 65
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 22+ cycles
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/mu[0m
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion
class: XSS
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/
confidence: 45
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH
testability: AUTH_HELPED
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF
class: SSRF
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list
confidence: 40
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained
testability: HUMAN_ONLY
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 28th consecutive converged cycle — passive DNS (getent, no HTTP) re-confirmed identical stable map, zero surface drift; no new unauth vector; "do not re-probe" constraints honored
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 22+ consecutive stable cycles
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback — do not re-probe
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN, live hosts byte-stable; no surface drift
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
## 2026-09-12 15:52:05 UTC [target] (model nemotron3)
[PRIO] pluto.portal.ipb.de,6.25,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=3,freshness=1  
[PRIO] nc.ipb.de,4.95,attack_surface=6,business_value=6,tech_exposure=6,gate_ease=3,cloud_surface=5,freshness=1  
[PRIO] gold.ipb.de,3.65,attack_surface=4,business_value=4,tech_exposure=3,gate_ease=6,cloud_surface=2,freshness=1  
[HYP] Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API  
class: IDOR  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/{id}/, /api/admin/multi-tenancy/v1/{user,tenant}/{id}/  
confidence: 65  
reasoning: DRF multi-tenancy API exposes tenant/user/membership/association-request/check-in objects with sequential integer IDs; all endpoints auth-gated (401 WWW-Authenticate: Token) but per-tenant authorization is sole BOLA control; EdgePortal bundle confirms extensive API surface; /api/system/ 401@58 byte-stable 22+ cycles  
evidence_needed: authenticated low-priv account reading another tenant's objects via ID manipulation (200 vs 403/404 differentiation)  
verify_steps: 1) Obtain low-priv credentialed session (Token) via admin-provisioned test account; 2) GET /api/multi-tenancy/v1/tenant/{other_id}/ with Authorization: Token header; 3) Observe 200 vs 403/404; repeat for /user/{id}/, /association-request/{id}/, /check-in/{id}/, /membership/{id}/, /admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET)  
impact: Cross-tenant data access → PII, membership, check-in, association-request dump across all tenants; CRITICAL  
testability: AUTH_HELPED  
[HYP] EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion  
class: XSS  
asset: pluto.portal.ipb.de /api/multi-tenancy/v1/user/profile-picture/{upload,download}/  
confidence: 45  
reasoning: EdgePortal SPA bundle references profile-picture upload/download endpoints (both 401 auth-gated); DRF multi-tenancy accepts file uploads; SVG upload with image/svg+xml content-type confusion could lead to stored XSS when rendered in victim's browser context; endpoints confirmed auth-gated (401) in prior probes  
evidence_needed: authenticated session; upload SVG with script payload; verify execution context on download/render  
verify_steps: 1) Obtain low-priv credentialed session (Token); 2) POST /api/multi-tenancy/v1/user/profile-picture/upload/ with multipart SVG containing <svg onload=alert(origin)>; 3) GET /api/multi-tenancy/v1/user/profile-picture/download/ → check Content-Type and execution; 4) Test CSP bypass via data: or blob: if direct render blocked  
impact: Stored XSS in authenticated tenant context → session theft, tenant data access; HIGH  
testability: AUTH_HELPED  
[HYP] NC AppAPI ExApp registry auth mis-scoping enabling SSRF  
class: SSRF  
asset: nc.ipb.de /ocs/v2.php/apps/app_api/apps/list  
confidence: 40  
reasoning: Live OCS caps 200 confirmed (requires OCS-APIRequest: true header; app_api 34.0.0 + bruteforce.delay=0); unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 (route exists, no session) vs /ocs/v2.php/cloud/apps → 401 confirms session-gating; app_api enables external app runtime historically exposing unauth endpoints/SSRF once registered; provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps  
evidence_needed: valid NC session; enumerate ExApps then probe each registered ExApp route unauth vs authed  
verify_steps: 1) Obtain valid NC session (HUMAN_ONLY); 2) GET /ocs/v2.php/apps/app_api/apps/list with OCS-APIRequest: true header and session; 3) For each returned ExApp route, GET unauth and authed → status diff; 4) If ExApp route reachable, test SSRF payloads (169.254.169.254) on ExApp endpoints  
impact: SSRF to cloud metadata (169.254.169.254) or internal services; ExApp auth bypass — HIGH if session obtained  
testability: HUMAN_ONLY  
[PARKED] NC AppAPI ExApp registry auth mis-scoping enabling SSRF: confidence 40 at threshold but HUMAN_ONLY blocker (no session path); provisioning_api/impersonate/oauth2/circles NOT confirmed by live caps — prior KC overstatement  
[FINAL] 1. Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (confidence 65) — CRITICAL impact, clear verify path with credentials, AUTH_HELPED testability  
[FINAL] 2. EdgePortal profile-picture uploaded-SVG stored-XSS via content-type confusion (confidence 45) — HIGH impact if upload accepts SVG, AUTH_HELPED testability, requires auth  
[NEXT] HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) via admin-provisioned test account (kiosk_login oracle parked — WAF risk, low impact); with that single token run cross-tenant seq-ID BOLA sweep on /api/multi-tenancy/v1/{user,tenant,association-request,membership,check-in}/1..N AND /api/admin/multi-tenancy/v1/{user,tenant}/{id}/ (read-only GET) + profile-picture SVG upload test
[LEARN] ACCEPTED MISC @ *.ipb.de: 29th consecutive converged cycle — passive DNS (getent, no HTTP) re-confirmed identical stable map, zero surface drift; triage/probe-results empty; no new unauth vector; "do not re-probe" constraints honored  
[LEARN] ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: unchanged — DRF multi-tenancy prime cross-tenant chokepoint, all seq-ID endpoints auth-gated, top priority, HUMAN_ONLY  
[LEARN] ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated, no unauth config/schema leak — unchanged, 22+ consecutive stable cycles  
[LEARN] ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live; provisioning_api/impersonate NOT confirmed by live caps — prior KC entry overstated  
[LEARN] ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path — unchanged  
[LEARN] REJECTED MISC @ event/www.ipb.de: pretix /control 403, /redirect allowlisted, .env/server-info 403 blocked — saturated, do not re-probe  
[LEARN] REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback — do not re-probe  
[LEARN] ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost  
[LEARN] ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same as piwik/webcam); login-only out-of-scope class  
[LEARN] ACCEPTED MISCONFIG @ *.ipb.de wildcard DNS masking: closed hypothesis, stable — my/prod/app/survey/guest.gold/focus.gold/auth.gold NXDOMAIN, live hosts byte-stable; no surface drift  
[RISK] ipb: 65 — pluto.portal.ipb.de Cross-tenant BOLA (confidence 65) remains sole validated high-impact hypothesis; nc.ipb.de Nextcloud app_api 34.0.0 confirmed live but ExApp surface session-gated (HUMAN_ONLY); wildcard DNS masking hypothesis killed; no credentialed access path for pluto BOLA verification; 5 live services behind wildcard proxy (Nextcloud, Jitsi, 2x Plesk, CIC) but all login-only or config-hardened; validated bugs: 0
