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
