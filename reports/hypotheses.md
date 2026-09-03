# Hypotheses (ranked)

## RANKED HYPOTHESES 2026-09-02 21:52:01 UTC

## RANKED HYPOTHESES 2026-09-02 23:42:04 UTC

## RANKED HYPOTHESES 2026-09-03 02:02:14 UTC

## RANKED HYPOTHESES 2026-09-03 07:15:19 UTC

## RANKED HYPOTHESES 2026-09-03 12:08:07 UTC

## RANKED HYPOTHESES 2026-09-03 16:37:18 UTC
- [60] pluto.portal.ipb.de: Multi-tenancy IDOR/BOLA across user/membership/tenant endpoints (from art/lead_bigpickle.txt)
- [60] *.ipb.de: Wildcard DNS masks real attack surface (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de,
- NEXT(hypotheses-bigpickle.txt): HUMANAUTH — need a valid account on pluto.portal.ipb.de to test the multi-tenant IDOR/BOLA surface (user/me, membership, tenant-relationship, user-token)
- LEARN: REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
- LEARN: REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
- LEARN: ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
- LEARN: REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
- LEARN: ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/.
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here.
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked).

## RANKED HYPOTHESES 2026-09-03 19:32:07 UTC
- [60] pluto.portal.ipb.de: Cross-tenant BOLA across membership/tenant/user endpoints on pluto.portal.ipb.de (from art/lead_nemotron3.txt)
- [60] pluto.portal.ipb.de: Cross-tenant BOLA across membership/tenant/user endpoints (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMANAUTH: sponsor a low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/ and /api/mult
- NEXT(hypotheses-nemotron3.txt): HUMANAUTH: sponsor/obtain a valid low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/ 
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle.
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
- LEARN: REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
- LEARN: REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
- LEARN: ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
- LEARN: REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
- LEARN: ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: ACCEPTED cross-tenant BOLA @ pluto.portal.ipb.de: DRF multi-tenancy API with sequential IDs, auth-gated but per-tenant auth is sole BOLA control

## RANKED HYPOTHESES 2026-09-03 21:55:25 UTC
- [60] pluto.portal.ipb.de: Cross-tenant BOLA across membership/tenant/user endpoints on pluto.portal.ipb.de (from art/lead_nemotron3.txt)
- [60] pluto.portal.ipb.de: Cross-tenant BOLA across membership/tenant/user endpoints (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de,
- NEXT(hypotheses-bigpickle.txt): HUMANAUTH: sponsor a low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/ and /api/mult
- LEARN: REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
- LEARN: REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
- LEARN: ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
- LEARN: REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
- LEARN: ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED cross-tenant BOLA @ pluto.portal.ipb.de: DRF multi-tenancy API with sequential IDs, auth-gated but per-tenant auth is sole BOLA control
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle.
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
- LEARN: REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
- LEARN: REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
- LEARN: ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
- LEARN: REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
- LEARN: ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: ACCEPTED cross-tenant BOLA @ pluto.portal.ipb.de: DRF multi-tenancy API with sequential IDs, auth-gated but per-tenant auth is sole BOLA control
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; all endpoints seq-ID, auth-gated; remains top priority pending
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle.
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
