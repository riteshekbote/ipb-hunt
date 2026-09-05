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

## RANKED HYPOTHESES 2026-09-03 23:44:25 UTC
- [60] pluto.portal.ipb.de: Cross-tenant BOLA across tenant/user/membership/association-request endpoints (from art/lead_bigpickle.txt)
- [60] pluto.portal.ipb.de: Cross-tenant BOLA across membership/tenant/user endpoints on pluto.portal.ipb.de (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de,
- NEXT(hypotheses-bigpickle.txt): HUMAN: sponsor a low-privilege credentialed account on pluto.portal.ipb.de to exercise cross-tenant BOLA on /api/multi-tenancy/v1/tenant/{id}/, /user/{id}/, /as
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
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: EdgePortal multi-tenancy is prime cross-tenant chokepoint; expanded surface (association-request, user-token, self end
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /schema, /swagger, /openapi all return SPA fallback, not schema/config leak; do not re-probe a
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.

## RANKED HYPOTHESES 2026-09-04 01:58:12 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in endpoints (from art/lead_nemotron3.txt)
- [60] pluto.portal.ipb.de: Cross-tenant BOLA across tenant/user/membership/association-request endpoints (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: HEAD https://eticket.ipb.de/ then HEAD https://nc.ipb.de/ then HEAD https://gold.ipb.de/ then HEAD https://piwik.ipb.de/ then HEAD https://survey.ipb.de/
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de,
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: EdgePortal multi-tenancy is prime cross-tenant chokepoint; expanded surface (association-request, user-token, self end
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan; 21 inventory hosts never individually re-confirmed post-discovery. Potential for h
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle. PARK
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /schema, /swagger, /openapi all return SPA fallback, not schema/config leak; do not re-probe a
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe.
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked).
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe

## RANKED HYPOTHESES 2026-09-04 06:54:36 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (from art/lead_nemotron3.txt)
- [55] pluto.portal.ipb.de: Self-service registration endpoints bypass HUMAN credential gate (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard fallback; repeat for auth.gold.ipb.de, clou
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://pluto.portal.ipb.de/_exceptions/user-register/ then GET /_exceptions/register-tenant/ then GET /_exceptions/forgot-password/ — highest-levera
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: _exceptions routes in SPA bundle are most promising path for self-service credential acquisition
- LEARN: ACCEPTED MISCONFIG @ eticket/nc/gold/piwik/webcam/cic.ipb.de: SSL cert failures confirm live hosts behind wildcard proxy
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ allowlisted
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 blocked

## RANKED HYPOTHESES 2026-09-04 11:58:22 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (from art/lead_nemotron3.txt)
- [55] pluto.portal.ipb.de: EdgePortal _exceptions registration routes enable self-service credential acquisition (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://pluto.portal.ipb.de/_exceptions/user-register/ then GET /_exceptions/register-tenant/ then GET /_exceptions/forgot-password/ — check Content-
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content-length, status vs wildcard fallback; repeat for nc.ipb.de, gold.ipb.de,
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: _exceptions routes in SPA bundle are most promising path for self-service credential acquisition
- LEARN: ACCEPTED MISCONFIG @ eticket/nc/gold/piwik/webcam/cic.ipb.de: SSL cert failures confirm live hosts behind wildcard proxy
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ allowlisted
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 blocked
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe

## RANKED HYPOTHESES 2026-09-04 15:31:05 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (from art/lead_nemotron3.txt)
- [55] nc.ipb.de: Nextcloud OCS provisioning_api + impersonate = privilege escalation vector (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, o
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content-length, status vs wildcard fallback; repeat for nc.ipb.de, gold.ipb.de,
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix responds with "Unknown host" — no custom domain configured, pretix handles wildcard
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all return SPA fallback (354606 bytes) = no server-side registration form; API endpoints auth-gated (use
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api, impersonate, oauth2, circles, WebDAV, OCS API; bruteforce delay=0; status.php full
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet with config.js public, anonymous guest domain (guest.gold.ipb.de), XMPP backend (auth.gold.ipb.de)
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token in meta tag
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services behind wildcard proxy (Nextcloud 34.0.3, Jitsi Meet, Plesk x2, custom CIC PHP)
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe

## RANKED HYPOTHESES 2026-09-04 18:43:28 UTC
- [55] nc.ipb.de: Cross-tenant BOLA via sequenti[0m (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://eticket.ipb.de/ (Host: eticket.ipb.de) — check Server header, content-length, status vs wildcard fallback; repeat for nc.ipb.de, gold.ipb.de,
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta

## RANKED HYPOTHESES 2026-09-04 21:05:20 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on multi-tenancy/user/check-in/association-request endpoints (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, o
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta

## RANKED HYPOTHESES 2026-09-04 22:54:15 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA on EdgePortal multi-tenancy API (from art/lead_bigpickle.txt)
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta

## RANKED HYPOTHESES 2026-09-05 00:33:13 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA on EdgePortal multi-tenancy API (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: obtain one attacker-owned tenant account on pluto.portal.ipb.de EdgePortal and test token-scoped cross-tenant seq-ID access on /api/multi-tenancy/v1/{ass
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, o
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta

## RANKED HYPOTHESES 2026-09-05 05:01:02 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (from art/lead_nemotron3.txt)
- [65] pluto.portal.ipb.de: Cross-tenant BOLA on EdgePortal multi-tenancy API (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: obtain one attacker-owned low-priv EdgePortal tenant account (pluto.portal.ipb.de) — then, using the now-known public OPTIONS schemas, test cross-tenant 
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://nc.ipb.de/ocs/v2.php/cloud/capabilities -H "OCS-APIRequest: true" — extract full enabled app list to confirm provisioning_api, impersonate, o
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta

## RANKED HYPOTHESES 2026-09-05 08:46:36 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (from art/lead_nemotron3.txt)
- [65] pluto.portal.ipb.de: Cross-tenant BOLA on EdgePortal multi-tenancy API (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) then (a) test cross-tenant seq-ID BOLA on /api/multi-tenancy/v1/{user,te
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.
- LEARN: CHANGED framework-recon @ nc.ipb.de: live OCS capabilities (curl -k, 200) shows NC 34.0.3 with bruteforce.delay=0 and app_api 34.0.0 ONLY; provisioning_api/impe
- LEARN: ACCEPTED framework-recon @ cic.ipb.de: self-hosted CIC, plain custom PHP user+pass self-POST form, no CSRF token, PHPSESSID; login-only (out-of-scope class)
- LEARN: ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000) this cycle — Jitsi anonymousdomain config-only, not a separate live vhost
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi root confirms unguessable random roomName rooms; anonymous guest by-design; no room-URL leak path
- LEARN: REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persis
- LEARN: CHANGED framework-recon @ nc.ipb.de: live OCS capabilities (curl -k, 200) shows NC 34.0.3 with bruteforce.delay=0 and app_api 34.0.0 ONLY; provisioning_api/impe
- LEARN: ACCEPTED framework-recon @ cic.ipb.de: self-hosted CIC, plain custom PHP user+pass self-POST form, no CSRF token, PHPSESSID; login-only (out-of-scope class)
- LEARN: ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000) this cycle — Jitsi anonymousdomain config-only, not a separate live vhost
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi root confirms unguessable random roomName rooms; anonymous guest by-design; no room-URL leak path
- LEARN: REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persis
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with provisioning_api/impersonate/oauth2/circles/WebDAV/OCS; bruteforce delay=0; status.php version leak;
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta

## RANKED HYPOTHESES 2026-09-05 12:17:02 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (from art/lead_nemotron3.txt)
- [40] nc.ipb.de: NC AppAPI ExApp auth mis-scoping (extend prior, 404 not 401) (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) and one low-priv NC session, then: (a) cross-tenant seq-ID BOLA on /api/
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: unauth GET /ocs/v2.php/apps/app_api/apps/list → 404 and /ocs/v2.php/cloud/apps → 401 — AppAPI ExApp list and provisioning 
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: both Plesk login.php → 303 (unchanged); public login panel = out-of-scope class; no new attack surface.
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonat
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak p
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
- LEARN: REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persis

## RANKED HYPOTHESES 2026-09-05 15:22:54 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: obtain one attacker-owned low-priv EdgePortal tenant token (pluto.portal.ipb.de) then (a) cross-tenant seq-ID BOLA on /api/multi-tenancy/v1/{user,tenant,
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoints)
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is low-severity enumeration; distinct error message per token validity confirmed in bundle. PARKED
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface uniformly auth-gated (401, WWW-Authenticate: Token); no unauth config/schema leak (all SPA
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: NC 34.0.3 with app_api 34.0.0 ONLY confirmed live via OCS capabilities; provisioning_api/impersonate/oauth2/circles NOT co
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi config.js public, anonymous guest by-design, unguessable roomName, no room-URL leak path
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 login.php → 303; public login panel = out-of-scope class
- LEARN: ACCEPTED framework-recon @ cic.ipb.de: self-hosted CIC, custom PHP login, no CSRF token, PHPSESSID; login-only out-of-scope class
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/, /sites, /schema/, /swagger, /openapi all SPA fallback; do not re-probe
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject class
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist; mature hardening; do not re-probe
- LEARN: REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000); wildcard mask persists
- LEARN: ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" (400); no custom domain configured
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonat
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak p
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
- LEARN: REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persis

## RANKED HYPOTHESES 2026-09-05 17:25:57 UTC
- [65] pluto.portal.ipb.de: Cross-tenant BOLA via sequential IDs on EdgePortal multi-tenancy API (from art/lead_nemotron3.txt)
- [60] *.ipb.de: Wildcard DNS masks real attack surface (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://app.ipb.de/ (Host: app.ipb.de) — check for live HTTP, Server header, distinct content vs wildcard; repeat for auth.gold.ipb.de, cloud.ipb.de,
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://app.ipb.de/ -H "Host: app.ipb.de" -k — check for distinct content vs wildcard fallback (1 rps, GET only); repeat for auth.gold.ipb.de, cloud.
- LEARN: REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
- LEARN: REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
- LEARN: REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
- LEARN: REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
- LEARN: ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
- LEARN: REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
- LEARN: ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: ACCEPTED cross-tenant BOLA @ pluto.portal.ipb.de: DRF multi-tenancy API with sequential IDs, auth-gated but per-tenant auth is sole BOLA control
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonat
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak p
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
- LEARN: REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persis
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonat
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak p
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
- LEARN: REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persis
- LEARN: ACCEPTED MISCONFIG @ cloud.ipb.de: resolves 194.29.230.41 → 3rd "I/P/B/ Cloudhosting Panel" Plesk login vhost (same panel as piwik/webcam); login-only out-of-sc
- LEARN: REJECTED MISC @ app/auth.gold/my/prod/survey/www.cic.ipb.de: no DNS this cycle — Host-header wildcard-probe approach non-executable; wildcard mask does not reso
- LEARN: REJECTED MISC @ mirror/spam/spam01/spam02/ns6/mail/speedtest.ipb.de: now resolve to distinct IPs but are non-web infra (mail/spam/NS) or public apt mirror (mirr
- LEARN: ACCEPTED BOLA-IDOR @ pluto.portal.ipb.de: DRF multi-tenancy is the prime cross-tenant chokepoint; expanded surface (association-request, user-token, self endpoi
- LEARN: ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface; 21 inventory hosts never 
- LEARN: ACCEPTED AUTH @ pluto.portal.ipb.de: kiosk_login token oracle is a low-severity enumeration; distinct error message per token validity confirmed in bundle
- LEARN: ACCEPTED framework-recon @ pluto.portal.ipb.de: full DRF data surface (incl. /api/system/, motd, check-in, association-request) uniformly auth-gated (401, WWW-A
- LEARN: REJECTED MISC @ pluto.portal.ipb.de: /api/config/v1/, /api/sites, /api/schema/, /swagger, /api/openapi all return SPA fallback, not schema/config leak; do not r
- LEARN: REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here
- LEARN: REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked)
- LEARN: REJECTED MISC @ event.ipb.de: pretix /control 403 and /redirect allowlist confirm mature hardening; do not re-probe
- LEARN: ACCEPTED MISCONFIG @ nc/gold/piwik/webcam/cic.ipb.de: 5 new live services confirmed behind wildcard proxy — Nextcloud 34.0.3 (nc), Jitsi Meet (gold), Plesk Pane
- LEARN: ACCEPTED MISCONFIG @ eticket.ipb.de: pretix "Unknown host" — no custom domain, wildcard handling
- LEARN: REJECTED AUTH @ pluto.portal.ipb.de _exceptions routes: all SPA fallback (354606), user-registration API 401, tenant-registration 403; self-service credential h
- LEARN: ACCEPTED framework-recon @ nc.ipb.de: Nextcloud 34.0.3 with app_api 34.0.0 confirmed live via OCS capabilities (bruteforce.delay=0); provisioning_api/impersonat
- LEARN: ACCEPTED framework-recon @ gold.ipb.de: Jitsi Meet config.js public, anonymous guest domain, XMPP backend; unguessable random roomName rooms; no room-URL leak p
- LEARN: ACCEPTED framework-recon @ piwik/webcam.ipb.de: Plesk Panel 18.0.80-6 with forgery_protection_token meta
- LEARN: REJECTED MISC @ *.{de-cix,kinski,hostmaster,track,spam,spam01,spam02,ns6,dns2,mail,moderated,focus}.ipb.de: all DNS-dead (000) this cycle — wildcard mask persis
- LEARN: ACCEPTED MISCONFIG @ guest.gold.ipb.de: does not resolve (000); Jitsi anonymousdomain config-only, not a live vhost
