# AUD-004 - URL Inventory, HTTP Status, Redirects, and Canonical Behavior

Status: PARTIAL - production crawl baseline captured; sitemap and Search Console merge pending  
Date: 2026-10-05  
Executor: ChatGPT with Julian

## Objective

Produce the authoritative inventory of production URLs and document status codes, redirects, canonical behavior, and important URL variants without modifying production.

## Authorization

Production crawl authorization was explicitly granted by Julian under OQ-04 with these limits:
- maximum 1 request/second
- concurrency 1
- maximum 500 URLs
- GET/HEAD only
- no forms
- no login
- no `wp-admin`
- no POST
- no artificially generated parameters

## Evidence

- `docs/audits/evidence/AUD-004/2026-10-05-production-crawl.csv`
- `docs/audits/evidence/AUD-004/2026-10-05-production-crawl-meta.txt`
- `docs/audits/evidence/AUD-004/2026-10-05-url-behavior-observations.md`

## Current URL inventory

| URL / pattern | Observed behavior | Canonical | Descriptive classification |
|---|---|---|---|
| `/` | 200 | self | keep as is |
| `/paquetes/` | 200 | self | keep as is |
| `/acerca-de/` | 200 | self | keep as is |
| `/acerca-de/historia/` | 200 | self | keep as is |
| `/contacto/` | 200 | self | keep as is |
| `/acerca-de/aviso-de-privacidad/` | 200 | none | keep/unknown canonical requirement |
| `/acerca-de/politica-de-devoluciones/` | 200 | none | keep/unknown canonical requirement |
| `/acerca-de/terminos-y-condiciones/` | 200 | none | keep/unknown canonical requirement |
| `/timeline/actualidad/` | 200 | none | unknown; requires sitemap/GSC value check |
| `/timeline/reconocimiento/` | 200 | none | unknown; requires sitemap/GSC value check |
| `/timeline/orgienes/` | 200 | none | unknown; requires sitemap/GSC value check |
| `/timeline/historia/` | 200 | none | unknown; requires sitemap/GSC value check |
| `/timeline/` | 200; byte-identical to home | home | redirect/drop candidate; final decision deferred |
| `/ctl-stories/timeline-stories/` | 200 | none | unknown; requires sitemap/GSC value check |
| `/mariachi-profesional-economico-en-cdmx/` | 301 to `/` | n/a | preserve redirect behavior |
| HTTPS non-www page variants | 301 to HTTPS www | final page dependent | preserve host normalization |
| no-trailing-slash page variants | 301 to trailing slash | final page dependent | preserve slash normalization |
| selected mixed-case main-page variants | 200 | lowercase URL | duplicate variant; redirect candidate |
| uppercase legal-page variants | 200 | none | duplicate variant; redirect candidate |
| HTTP root variants | 200 Plesk default page | none observed | incorrect current behavior; migration must normalize |
| HTTP internal paths | 404 | none | incorrect current behavior; migration must normalize |

The classifications above are descriptive audit labels only. They are not implementation decisions or redirect rules.

## Key findings

1. The anchor crawl completed within authorization and discovered 21 requested URLs, but public WordPress-generated URLs exist outside that link graph.
2. HTTPS non-www consolidates to HTTPS www with 301 for tested page URLs.
3. Trailing-slash normalization works for tested canonical HTTPS page URLs.
4. HTTP does not consolidate to HTTPS: roots serve the Plesk default page with 200 and tested internal paths return 404.
5. Internal links to the HTTPS non-www host are widespread and create unnecessary internal redirects.
6. Three legal pages have no canonical, and their uppercase variants also return 200 without canonical.
7. Four public Cool Timeline Story URLs and one public `ctl-stories` taxonomy URL were confirmed outside the initial crawl; all require later value/indexation review.
8. `/timeline/` is a 200 alias whose body is byte-identical to the home page and whose canonical points to the home page.
9. The WordPress front-page slug redirects 301 to `/`.
10. One attachment-page sample redirects 301 to its media asset; all attachment pages have not been exhaustively tested.

## Completeness gap

Per `docs/AUDIT-PLAN.md`, AUD-004 completion requires merging:
- production crawl URLs
- sitemap URLs from AUD-005
- Search Console URL data from AUD-006

Those inputs are not yet available. This package must therefore remain partial and be revisited after AUD-005 and AUD-006.

## Production changes

None. All work in this package to date was read-only.
