# Knowledge Base (seed)
- 2026-09-03 REJECTED IDOR @ my.ipb.de: no live HTTP confirmed, confidence < 40, requires auth
- 2026-09-03 REJECTED OATH @ auth.gold.ipb.de: no live HTTP confirmed, confidence < 40, passive-only verify may 404
- 2026-09-03 ACCEPTED MISCONFIG @ *.ipb.de: wildcard DNS confirmed by dedicated deep scan (0 genuinely dedicated hosts), hides real attack surface
- 2026-09-03 ACCEPTED framework recon @ pluto.portal.ipb.de — DRF with multi-tenancy; auth required, CSRF via /api/ct/
- 2026-09-03 REJECTED open-redirect @ event.ipb.de — /redirect/ validates against fixed allowlist
- 2026-09-03 ACCEPTED framework-recon AUTH @ pluto.portal.ipb.de: DRF multi-tenancy, all data endpoints auth-gated, CSRF via unbound /api/ct/.
- 2026-09-03 REJECTED open-redirect @ event.ipb.de: /redirect/ enforces fixed allowlist; reject open-redirect class here.
- 2026-09-03 REJECTED config-exposure @ www.ipb.de: .env/server-info 403 (blocked).
