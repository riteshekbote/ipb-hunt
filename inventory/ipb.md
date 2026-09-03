# IPB Internet Provider in Berlin GmbH inventory (discovery seed 2026-09-02)
# NOTE: hosts below are discovery candidates from passive DNS/CT; confirm in-scope vs program scope before active testing.
app.ipb.de
auth.gold.ipb.de
cic.ipb.de
cloud.ipb.de
de-cix.ipb.de
dns2.ipb.de
eticket.ipb.de
event.ipb.de
focus.gold.ipb.de
gold.ipb.de
guest.gold.ipb.de
hostmaster.ipb.de
ipb.de
kinski.ipb.de
mail.ipb.de
mirror.ipb.de
moderated.gold.ipb.de
my.ipb.de
nc.ipb.de
ns6.ipb.de
piwik.ipb.de
pluto.portal.ipb.de
prod.ipb.de
spam.ipb.de
spam01.ipb.de
spam02.ipb.de
speedtest.ipb.de
survey.ipb.de
track.ipb.de
webcam.ipb.de
www.cic.ipb.de
www.ipb.de
www.survey.ipb.de

## PASSIVE RECON 2026-09-02 (read-only, non-intrusive)

> Recon observations only. These are NOT confirmed vulnerabilities; ownership/in-scope of each host must be confirmed against the program scope before any active testing. Hosts resolve + serve HTTP — investigation requires scoped authorization.

**Probed:** 33 hosts | **Live HTTP:** 0

| Host | Status | Server/Tech |
|---|---|---|

## 2026-09-02 21:52:01 UTC

## 2026-09-02 23:42:04 UTC

## 2026-09-03 02:02:14 UTC

## 2026-09-03 07:15:19 UTC

## 2026-09-03 12:08:07 UTC

## 2026-09-03 16:37:18 UTC
- NEW pluto.portal.ipb.de live — React SPA backed by DRF multi-tenancy API (200)
- NEW event.ipb.de live — pretix ticketing (200), nginx/1.31.4
- NEW www.ipb.de live — PHP/8.3.33 on PleskLin (200)
- NEW ipb.de → 301 → www.ipb.de
- CHANGED `.env`/`server-info` on www.ipb.de → 403 (blocked, not exposed)
- NEW pluto.portal.ipb.de live — Django REST Framework multi-tenant customer portal (React SPA)
- NEW event.ipb.de live — pretix ticketing (nginx/1.31.4)
- NEW www.ipb.de live — PHP/8.3.33 on PleskLin
- NEW ipb.de → 301 → www.ipb.de
- CHANGED `/.env`, `/server-info` on www.ipb.de → 403 (blocked, not exposed)

## 2026-09-03 19:32:07 UTC
- NEW pluto.portal.ipb.de live — React SPA backed by DRF multi-tenancy API (HTTP 200)
- NEW event.ipb.de live — pretix ticketing (HTTP 200), nginx/1.31.4
- NEW www.ipb.de live — PHP/8.3.33 on PleskLin (HTTP 200)
- NEW ipb.de → 301 → www.ipb.de
- CHANGED `/.env`, `/server-info` on www.ipb.de → 403 (blocked, not exposed)
- NEW app.ipb.de, auth.gold.ipb.de, my.ipb.de, prod.ipb.de, cloud.ipb.de — DNS resolution failure (Name or service not known) or SSL cert verify failed on probe attempts

## 2026-09-03 21:55:25 UTC
- NEW pluto.portal.ipb.de live — React SPA backed by DRF multi-tenancy API (HTTP 200), extensive /api/multi-tenancy/v1/, /api/admin/, /api/check-in/ endpoints discovered in bundle
- NEW event.ipb.de live — pretix ticketing (HTTP 200), nginx/1.31.4, /control 403, /redirect/ allowlisted
- NEW www.ipb.de live — PHP/8.3.33 on PleskLin (HTTP 200)
- NEW ipb.de → 301 → www.ipb.de
- CHANGED `/.env`, `/server-info` on www.ipb.de → 403 (blocked, not exposed)
- NEW app.ipb.de, auth.gold.ipb.de, my.ipb.de, prod.ipb.de, cloud.ipb.de — DNS resolution failure (Name or service not known) or SSL cert verify failed on probe attempts
- CHANGED Dedicated deep scan confirms wildcard DNS on *.ipb.de — 0 genuinely dedicated hosts after shared-IP filtering
