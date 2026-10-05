# AUD-004 Evidence - URL Behavior Observations

Date: 2026-10-05  
Mode: read-only production inspection  
Authorization: OQ-04 limits recorded in `docs/PROJECT-STATE.md`

## Crawl baseline

The authorized crawl started at `https://www.mariachisenciudaddemexico.com/` and completed with 21 requested URLs. The crawler used concurrency 1, a 1 second delay between requests, GET only, internal anchor links only, no query strings, no forms, no login, no `wp-admin`, and no POST requests.

Evidence:
- `2026-10-05-production-crawl.csv`
- `2026-10-05-production-crawl-meta.txt`

Important CSV interpretation: the `status` column is the final response status after any recorded redirect chain. For example, a non-www request may show status 200 while `redirect_chain` records the preceding 301.

## Main crawl findings

- 21 requested URLs were crawled: 15 unique final URLs, including HTML pages and directly linked image assets.
- HTTPS non-www page URLs discovered by the crawl redirected with 301 to the HTTPS www host.
- No multi-hop redirect chain was observed in the crawl sample.
- Three legal pages returned 200 but declared no canonical:
  - `/acerca-de/aviso-de-privacidad/`
  - `/acerca-de/politica-de-devoluciones/`
  - `/acerca-de/terminos-y-condiciones/`
- Internal links to the non-www HTTPS host are present across the site, including navigation/legal links and some media URLs. These cause avoidable internal 301 redirects.

## URL variant behavior

### Host and protocol

- Canonical production host observed: `https://www.mariachisenciudaddemexico.com/`.
- HTTPS apex/non-www requests for tested page URLs redirect 301 to HTTPS www.
- HTTP root on both apex and www responds 200 with the Plesk default page rather than redirecting to HTTPS.
- HTTP internal paths tested return 404 rather than redirecting to HTTPS.
- This confirms the HTTP anomaly already established in AUD-003 and makes protocol normalization a migration requirement.

### Trailing slash

For tested canonical HTTPS www page URLs, the no-trailing-slash variant redirects 301 to the trailing-slash URL.

### Case variants

The following tested mixed/uppercase variants respond directly 200 and canonicalize to lowercase:
- `/Paquetes/` -> canonical `/paquetes/`
- `/ACERCA-DE/` -> canonical `/acerca-de/`
- `/Contacto/` -> canonical `/contacto/`

The three legal-page uppercase variants respond directly 200 and have no canonical:
- `/ACERCA-DE/AVISO-DE-PRIVACIDAD/`
- `/ACERCA-DE/POLITICA-DE-DEVOLUCIONES/`
- `/ACERCA-DE/TERMINOS-Y-CONDICIONES/`

These are duplicate-address variants without a canonical signal.

Timeline uppercase behavior is different:
- `/TIMELINE/ACTUALIDAD/` -> 301 to `/timeline/actualidad/`
- `/TIMELINE/RECONOCIMIENTO/` -> 301 to `/timeline/reconocimiento/`
- `/TIMELINE/ORGIENES/` -> 301 to `/timeline/orgienes/`
- `/TIMELINE/HISTORIA/` -> 301 to `/acerca-de/historia/`

## WordPress URL sources not discovered by the anchor crawl

Read-only WP-CLI inspection identified:
- 8 published pages.
- 0 published posts.
- 7 published `elementor_library` records.
- 4 published `cool_timeline` records.
- 96 attachments in status `inherit`.

A tested Elementor template (`elementor_library`, ID 2139) returned no permalink through `wp post url`. A public frontend URL for Elementor templates is therefore not confirmed by this evidence.

### Cool Timeline single URLs

Four public Timeline Story permalinks were confirmed and each returned 200:
- `/timeline/actualidad/`
- `/timeline/reconocimiento/`
- `/timeline/orgienes/`
- `/timeline/historia/`

In the direct checks, these four normal lowercase Timeline Story URLs did not declare a canonical.

WordPress rewrite inspection confirmed the dedicated rule:
`timeline/([^/]+)(?:/([0-9]+))?/?$ -> index.php?cool_timeline=$matches[1]&page=$matches[2]`

### Cool Timeline archive alias

`/timeline/` returns 200, has the home-page title, declares canonical `https://www.mariachisenciudaddemexico.com/`, and its response body was byte-identical to the home page in the comparison:
- same byte length: 288908
- same SHA-256: `90113ef2d3d7e0ec298bdeecc1add8487430dfe3953bd347b2df286a308f46d3`

This is an alias/duplicate route for the home page rather than an independent content page.

### Cool Timeline taxonomy

The public taxonomy `ctl-stories` exists with one term:
- name: `Timeline Stories`
- slug: `timeline-stories`
- count: 4

Public URL confirmed:
`/ctl-stories/timeline-stories/`

Observed behavior:
- status 200
- no canonical
- title: `Timeline Stories Archivos – MARIACHI PROFESIONAL ECONÓMICO EN CDMX Email`

WordPress rewrite inspection confirmed:
`ctl-stories/([^/]+)/?$ -> index.php?ctl-stories=$matches[1]`

### Front-page internal slug

The published page used as the front page has slug:
`mariachi-profesional-economico-en-cdmx`

Its direct URL:
`/mariachi-profesional-economico-en-cdmx/`

redirects 301 to the site root `/`.

### Attachment behavior sample

WordPress reported 96 attachments with status `inherit`.

Sample attachment ID 2544:
- title: `Paquete 1 Hr Sab - Dom - Febrero 2025`
- MIME type: `image/jpeg`
- media URL: `/wp-content/uploads/2025/02/Paquete-1-Hr-Sab-Dom-Febrero-2025.jpg`
- WordPress attachment permalink: `/mariachi-profesional-economico-en-cdmx/paquete-1-hr-sab-dom-febrero-2025/`

The attachment permalink redirects 301 directly to the JPG asset. This is one verified sample and is not evidence that all 96 attachments behave identically.

## Current interpretation

The crawl alone is not an authoritative complete URL inventory. It missed public WordPress-generated routes that were not linked through the crawled anchors, specifically Timeline Story singles and the `ctl-stories` taxonomy archive.

AUD-004 therefore remains incomplete until its soft-dependency inputs are merged:
- sitemap URLs from AUD-005
- Search Console URL data from AUD-006

No redirect, canonical, WordPress, server, DNS, or production configuration was modified during these checks.
