# Audit Plan

Status: DRAFT — pending approval by Julián Cely.
Owner: Claude (audit planning lead).
Governance: `docs/00-START-HERE.md`, `docs/PROJECT-STATE.md`, `docs/CONTEXT-PROTOCOL.md`.

## Purpose

Define the pre-implementation audit for migrating https://mariachisenciudaddemexico.com from WordPress + Elementor Pro to Cloudflare Pages / Workers.

The audit establishes verified facts about SEO equity, URLs, tracking, conversion paths, DNS, email, and technical dependencies. No redesign, architecture, or implementation decision may be made before the relevant audit evidence exists (see Deferred Decisions).

This document is planning only. It authorizes no execution.

## Known Project Facts

Source: Julián Cely task brief and repository governance documents.

- F-01 Production site: https://mariachisenciudaddemexico.com.
- F-02 Current stack: WordPress + Elementor Pro.
- F-03 Target architecture: Cloudflare Pages / Workers.
- F-04 Staging project: https://mariachisenciudaddemexico.pages.dev.
- F-05 Production must remain untouched during development.
- F-06 No production DNS, nameserver, WordPress, tracking, or deployment changes are authorized (`PROJECT-STATE.md`).
- F-07 Staging must remain non-indexable during development.
- F-08 Julián states the site has approximately 4 years of accumulated SEO authority (magnitude unverified; see A-01).
- F-09 Properties to preserve where applicable: Search Console, GA4, Google Tag Manager, Google Ads, conversion tracking, forms and lead-generation flows, DNS records, MX, SPF, DKIM, DMARC, domain verification records, email continuity.
- F-10 Preferred target is Cloudflare Free plan when practical. Cost must not override SEO, reliability, email continuity, tracking, or migration safety.
- F-11 Roles: Julián Cely = final authority; Claude = audit and planning lead; ChatGPT = execution orchestrator, documentation custodian, integrator; other agents TBD.
- F-12 Current phase: PHASE 0 — Documentation bootstrap. Audit state: PLANNING.
- F-13 The repository is the source of truth; conversations are not (`CONTEXT-PROTOCOL.md`).
- F-14 Repository state at plan time: only `README.md` (empty), `docs/00-START-HERE.md`, `docs/PROJECT-STATE.md`, `docs/CONTEXT-PROTOCOL.md` exist.

## Assumptions Requiring Verification

None of these are facts. Each is verified by the listed work package.

| ID | Assumption | Verified by |
|---|---|---|
| A-01 | The site has meaningful organic traffic and rankings worth preserving at URL level. | AUD-006 |
| A-02 | Domain registrar, nameserver host, and DNS zone host are identifiable and accessible to Julián. | AUD-001, AUD-002 |
| A-03 | Email for the domain is in use and depends on MX/SPF/DKIM/DMARC records. | AUD-002 |
| A-04 | Cloudflare is not currently in front of production (or, if it is, its configuration is unknown). | AUD-003 |
| A-05 | Production runs a WordPress theme/plugin set that includes SEO, caching, forms, and tracking plugins. | AUD-003, AUD-008 |
| A-06 | Lead capture uses one or more of: forms, WhatsApp links, phone links, email links. | AUD-010 |
| A-07 | GA4, GTM, Google Ads, and Search Console are all in use and Julián has sufficient access. | AUD-001, AUD-011 |
| A-08 | Conversion actions in Google Ads are imported from GA4 or fired through GTM. | AUD-011 |
| A-09 | The site is content/lead-generation oriented with no e-commerce or user accounts. | AUD-003, AUD-008 |
| A-10 | Existing redirects exist (server, plugin, or Elementor level) and some URLs have external backlinks. | AUD-004, AUD-006 |
| A-11 | The staging project is currently not indexable. | AUD-014 |
| A-12 | Site size is small enough for a full crawl to be practical. | AUD-004 |
| A-13 | Forms depend on WordPress server-side processing and/or third-party services that need Workers-side replacements. | AUD-010, AUD-012 |
| A-14 | Consent/privacy banner behavior exists and affects tracking firing. | AUD-011 |

## Audit Principles

1. Read-only on all production systems unless Julián explicitly authorizes otherwise per task.
2. Evidence before decisions. No implementation or architecture decision from assumptions.
3. One approved work package at a time (`CONTEXT-PROTOCOL.md` Task Execution Rule). No automatic continuation.
4. High-risk discovery first: DNS, email, tracking, and SEO inventory precede any design or build work.
5. No invention. Missing or contradictory data is recorded as an open question and escalated to Julián.
6. Preserve over improve. The audit records the current state; it does not propose fixes unless a package says so.
7. Human-executed steps follow the sequential atomic format (Action, Location, Expected Result), one step at a time.
8. No secrets in the repository. Credentials, tokens, and API keys are never captured as evidence.
9. Token economy: findings are concise, structured, and reference evidence rather than restating it.
10. Each completed package is recorded in the repository before it is considered complete.

## Audit Scope

In scope (read-only observation): production site and its public behavior, WordPress admin (read-only), hosting panel, DNS zone, registrar, email provider settings, Search Console, GA4, GTM, Google Ads, staging pages.dev project, Cloudflare account state.

Out of scope for the audit: any change to production or third-party accounts, redirect creation, deployment, redesign, content rewriting, new tracking setup, SEO improvements, Cloudflare plan purchase.

Domain-to-package coverage (the 47 required domains):

| Domains | Package |
|---|---|
| Access prerequisites (all) | AUD-001 |
| 30 DNS, 31 nameservers, 32 MX, 33 SPF, 34 DKIM, 35 DMARC, 36 verification records, 37 email provider | AUD-002 |
| 1 architecture, 4 hosting/origin, 5 Cloudflare current state | AUD-003 |
| 6 structure, 7 URL inventory, 8 HTTP status, 9 redirects, 10 canonicals | AUD-004 |
| 11 robots.txt, 12 sitemaps, 13 indexability | AUD-005 |
| 14 Search Console baseline, 15 organic landing pages, 16 queries, 20 backlinks | AUD-006 |
| 17 structured data, 18 metadata, 19 internal linking, 21 content inventory | AUD-007 |
| 2 WordPress configuration, 3 Elementor usage, 42 security dependencies (WordPress side) | AUD-008 |
| 22 media/assets | AUD-009 |
| 23 forms, 24 conversion paths | AUD-010 |
| 25 GA4, 26 GTM, 27 Google Ads, 28 conversion actions, 29 consent/privacy | AUD-011 |
| 38 third-party integrations, 42 security dependencies (external) | AUD-012 |
| 39 performance, 40 Core Web Vitals, 41 accessibility | AUD-013 |
| 43 Pages/Workers compatibility, 44 staging non-indexation, 45 deployment dependencies (platform side) | AUD-014 |
| 45 deployment dependencies, 46 rollback, 47 post-migration validation | AUD-015 |
| Consolidation | AUD-016 |

## Work Package Sequence

Packages execute one at a time after individual approval. Order reflects risk, not parallelism.

| Stage | Packages | Rationale |
|---|---|---|
| A. Access and irreversible-risk baseline | AUD-001, AUD-002, AUD-003 | Confirm access first. Capture DNS/email state (hardest to repair if lost) and origin behavior before anything else. |
| B. Search equity discovery | AUD-004, AUD-005, AUD-006 | Establish URL, redirect, indexability, and organic-value baselines. |
| C. Content and conversion discovery | AUD-007, AUD-008, AUD-009, AUD-010, AUD-011, AUD-012 | Inventory what must be reproduced and what tracking/lead flows must survive. |
| D. Platform fit and performance | AUD-013, AUD-014 | Measure baselines; test what Cloudflare Pages / Workers can and cannot replicate. |
| E. Requirements and closure | AUD-015, AUD-016 | Derive rollback and validation requirements from evidence; consolidate and review exit criteria. |

Sequencing constraints beyond the table are listed in Dependencies. Packages within a stage may be reordered by Julián; cross-stage order must hold.

## Audit Work Packages

Executor labels: Julián, ChatGPT, Claude, coding agent, SEO agent, browser/research agent, TBD. "Suggested" means a proposal pending Julián's approval. Every package that touches production is read-only. Evidence and output paths use `docs/audits/`, which does not yet exist; creation is deferred to the first approved execution (see Open Questions OQ-02).

### AUD-001 — Access and ownership inventory

- Objective: Establish which systems exist and what read-only access each executor has, before any other audit work.
- Scope: Registrar, DNS host, hosting/VPS panel, WordPress admin, Cloudflare account, Search Console, GA4, GTM, Google Ads, email provider admin, staging project, any third-party services referenced by the site.
- Inputs required: Julián's knowledge of accounts and owners.
- Execution method: Julián confirms, per system, existence, owner account, and whether read-only/viewer access can be granted or exports produced. Guided one step at a time.
- Evidence to capture: System name, provider, owner account identifier (no credentials), access level available, access gaps. No passwords, tokens, or recovery codes.
- Expected output: Access matrix with gaps flagged.
- Dependencies: None.
- Completion criteria: Every system in scope is marked exists / does not exist / unknown, with an access level and an owner. Gaps are listed as open questions.
- Risks / cautions: Never share credentials in chat or repo. Prefer viewer roles or exported reports. Discovering a system with no recoverable owner is a critical risk.
- Suggested executor: Julián (facts); ChatGPT (matrix documentation).

### AUD-002 — DNS, nameserver, and email-authentication baseline

- Objective: Capture a complete, authoritative snapshot of the domain's DNS and email configuration so continuity can be guaranteed and any later change validated against it.
- Scope: Registrar record, delegated nameservers, full DNS zone, MX, SPF, DKIM selectors, DMARC, domain verification records (Google, Search Console, Ads, others), CAA, any subdomains in use, email provider dependencies, TTLs.
- Inputs required: Zone export or DNS panel view from the current DNS host; registrar nameserver view; email provider documentation of required records.
- Execution method: Read-only. Zone export or screenshots from the DNS host; public DNS queries for cross-check (`dig`/DNS lookup) against the live resolver; compare panel versus public answers. Identify which records belong to which service.
- Evidence to capture: Raw zone export; public query outputs with timestamp; registrar nameserver screenshot; list mapping each record to a service or "unknown".
- Expected output: DNS and email baseline record with each record classified (web, email, verification, other, unknown) and criticality.
- Dependencies: AUD-001.
- Completion criteria: Every record in the zone is accounted for or flagged unknown; DKIM selectors identified or flagged; panel and public views reconciled; email provider identified.
- Risks / cautions: Unknown records must not be assumed obsolete. DKIM selectors may be invisible without provider information. Panel exports can omit records from other DNS layers. Do not change any record.
- Suggested executor: Julián (exports); Claude (analysis); coding agent for public DNS queries (TBD).

### AUD-003 — Production architecture, hosting, and origin behavior

- Objective: Document how production is served end to end.
- Scope: Hosting/VPS, web server, PHP version, CDN or proxy layers, Cloudflare presence if any, TLS certificate, response headers, caching layers, www/non-www and http/https behavior, server-level redirects and rules.
- Inputs required: Public site access; hosting panel read-only access (AUD-001); DNS baseline (AUD-002).
- Execution method: Read-only HTTP header inspection of representative URLs; certificate inspection; hosting panel review; identify caching and security layers.
- Evidence to capture: Header captures per representative URL, certificate details, server/stack identification, list of layers between visitor and origin.
- Expected output: Architecture description with a diagram or table of layers and behaviors that Cloudflare must reproduce or replace.
- Dependencies: AUD-001, AUD-002.
- Completion criteria: Request path is documented for www/non-www/http/https variants; any proxy/CDN is identified or confirmed absent; origin behaviors that affect SEO are listed.
- Risks / cautions: Limit request volume. Do not trigger security tooling lockouts. No configuration changes.
- Suggested executor: coding agent or Claude with Julián for panel access (TBD).

### AUD-004 — URL inventory, HTTP status, redirects, and canonical behavior

- Objective: Produce the authoritative inventory of production URLs and how each behaves.
- Scope: Site structure; all crawlable URLs; status codes; redirect chains; canonical tags; URL variants (trailing slash, case, parameters, www, http); orphan or non-linked URLs discovered through sitemaps or Search Console.
- Inputs required: Crawl of production (read-only); sitemaps (AUD-005); Search Console URL data (AUD-006) where available.
- Execution method: Rate-limited crawl; merge with sitemap URLs and Search Console URLs; fetch status and redirect target per URL; extract canonical.
- Evidence to capture: Crawl export (URL, status, final URL, redirect chain, canonical, indexability signals, title, depth); crawl configuration and date.
- Expected output: URL inventory table with classification per URL (keep as is / redirect / drop candidate / unknown). Classification here is descriptive only; no redirect rules are written.
- Dependencies: AUD-003; soft dependency on AUD-005 and AUD-006 for completeness (re-run or merge if those complete later).
- Completion criteria: Inventory covers sitemap, crawl, and Search Console URL sources; every URL has status, canonical, and redirect data; existing redirects and chains are listed; discrepancies between sources are flagged.
- Risks / cautions: Crawl load on production. Parameter and pagination URLs can inflate the inventory. JavaScript-rendered links may be missed. Do not create or modify redirects.
- Suggested executor: coding agent or SEO agent (TBD).

### AUD-005 — robots.txt, sitemaps, and indexability controls

- Objective: Record all current crawl and index directives on production.
- Scope: robots.txt; XML sitemap index and child sitemaps; meta robots; X-Robots-Tag headers; noindex usage; hreflang if present; indexability of key templates.
- Inputs required: Public production access; plugin settings view (AUD-008) where relevant.
- Execution method: Fetch and archive robots.txt and sitemaps; list directives per template; compare sitemap URLs against crawl results.
- Evidence to capture: Verbatim robots.txt, sitemap files or URL lists with lastmod, directive findings per URL class.
- Expected output: Indexability baseline: what is indexable, blocked, noindexed, or listed in sitemaps, with inconsistencies flagged.
- Dependencies: AUD-003; coordinates with AUD-004.
- Completion criteria: robots.txt and every sitemap archived; sitemap versus crawl versus indexability mismatches listed.
- Risks / cautions: Sitemaps generated dynamically by plugins may differ from what the replacement site must produce. Archive timestamps.
- Suggested executor: coding agent or SEO agent (TBD).

### AUD-006 — Search Console baseline, organic landing pages, queries, and external value

- Objective: Quantify the SEO equity to protect.
- Scope: Search Console property configuration and verification method; performance baseline (clicks, impressions, CTR, position) over a defined window; top landing pages; top queries; page indexing status report; sitemaps submitted; links report; manual actions and security issues; other externally valuable URLs.
- Inputs required: Search Console access (AUD-001); date window chosen by Julián.
- Execution method: Export reports (performance by page and query, indexing, links, sitemaps). Optional supplementary backlink data from external tools only if approved.
- Evidence to capture: Report exports with dates and filters; property type (domain versus URL-prefix); verification methods in use.
- Expected output: Ranked list of high-value URLs and queries; indexing status summary; list of externally linked URLs; identified risks.
- Dependencies: AUD-001; informs AUD-004.
- Completion criteria: High-value URL list approved by Julián as the preservation priority set; verification methods recorded for AUD-002; exports archived.
- Risks / cautions: Search Console data is capped and sampled. Window choice affects conclusions; record it. Do not change settings, submit sitemaps, or use removal tools.
- Suggested executor: Julián (exports); SEO agent or Claude (analysis) (TBD).

### AUD-007 — Content, metadata, structured data, and internal linking inventory

- Objective: Inventory what content and on-page SEO elements must be reproduced.
- Scope: Page content inventory by template/type; titles; meta descriptions; headings; Open Graph/Twitter metadata; structured data (JSON-LD and microdata) and its validity; internal link graph and navigation; breadcrumbs.
- Inputs required: Crawl data (AUD-004); production access.
- Execution method: Extract on-page fields at scale from the crawl; validate structured data; map internal links and navigation structure.
- Evidence to capture: Extraction tables; structured data samples per type; internal link counts per URL; navigation structure.
- Expected output: Content and metadata inventory with templates identified, structured data types listed, and high-link-equity pages flagged.
- Dependencies: AUD-004; prioritization input from AUD-006.
- Completion criteria: Every inventoried URL has title, description, H1, and structured data status recorded; templates enumerated; internal linking hubs identified.
- Risks / cautions: Large crawls can over-collect. Content copied into the repo must be limited to what is needed (see Evidence Standards).
- Suggested executor: coding agent or SEO agent (TBD).

### AUD-008 — WordPress configuration, Elementor usage, and security dependencies (WordPress side)

- Objective: Determine what WordPress and Elementor Pro provide that the new architecture must replicate or retire.
- Scope: WordPress and PHP versions; active theme and child theme; plugin inventory with purpose; SEO plugin configuration; caching/performance plugins; security plugins; Elementor Pro features in use (widgets, forms, popups, dynamic content, theme builder templates, global styles); custom code (functions, snippets, custom CSS/JS); custom post types; user roles; scheduled tasks.
- Inputs required: WordPress admin read-only view or exports (AUD-001).
- Execution method: Read-only review of Plugins, Theme, Elementor settings, Templates, Site Health. Screenshots or exports.
- Evidence to capture: Plugin list with versions and status; Elementor feature usage list; custom code excerpts location; Site Health report.
- Expected output: Dependency register: each feature classified as content only, behavior to replicate, behavior to retire, or unknown.
- Dependencies: AUD-001, AUD-003.
- Completion criteria: All active plugins and Elementor Pro features in use are classified; custom code is located and described.
- Risks / cautions: Do not update, deactivate, or save anything in WordPress. Admin screens can trigger writes; use view-only paths. Do not capture license keys or API keys.
- Suggested executor: Julián (access) with ChatGPT or Claude analysis; coding agent only if exports are provided (TBD).

### AUD-009 — Media and assets inventory

- Objective: Inventory media and static assets to migrate.
- Scope: Images, video, audio, documents, fonts, icons; file sizes and formats; usage per page; off-site hosted media (embeds, third-party video); upload directory structure and indexed media URLs.
- Inputs required: Crawl data; WordPress media library view; Search Console image data if available.
- Execution method: Extract asset URLs from the crawl; reconcile against media library; identify indexed or externally linked asset URLs.
- Evidence to capture: Asset table (URL, type, size, pages using it, external-hosted flag); total weight per page type.
- Expected output: Asset inventory with high-value, unused, and off-site assets distinguished.
- Dependencies: AUD-004, AUD-008.
- Completion criteria: Every asset referenced by an inventoried page is listed; licensing or ownership uncertainties flagged.
- Risks / cautions: Hotlinked and third-party assets may disappear. Image URLs that rank or have backlinks may need preservation.
- Suggested executor: coding agent (TBD).

### AUD-010 — Forms, lead-generation flows, and conversion paths

- Objective: Document every way a visitor can contact the business and how each is processed and measured.
- Scope: Forms (fields, validation, anti-spam, submission handling, notifications, storage, confirmations, thank-you pages); WhatsApp links/buttons; click-to-call links; email links; chat widgets; floating buttons; other contact paths; destinations of leads.
- Inputs required: Production site; Elementor and form plugin settings (AUD-008); GTM and GA4 view (AUD-011).
- Execution method: Read-only inspection of each path in the DOM and settings. Any live submission test requires explicit separate authorization from Julián and a defined test record procedure.
- Evidence to capture: Path inventory (page, element, type, destination); form definitions; notification routing; thank-you URLs.
- Expected output: Conversion path register with each path linked to its tracking event (once AUD-011 completes) and business criticality.
- Dependencies: AUD-008; links to AUD-011.
- Completion criteria: Every conversion path on every template is listed with destination and processing mechanism; lead destination systems identified.
- Risks / cautions: Test submissions create real leads and may fire real conversions. Do not submit without authorization. Do not copy personal data of past leads into the repo.
- Suggested executor: Julián and browser/research agent (TBD).

### AUD-011 — GA4, GTM, Google Ads, conversion actions, and consent behavior

- Objective: Baseline all measurement so continuity can be verified after migration.
- Scope: GA4 properties, data streams, measurement IDs, events, key events, audiences, links to Ads/Search Console; GTM containers, tags, triggers, variables, versions; Google Ads account, linked properties, conversion actions (source, counting, value, status), tag firing; other pixels; consent/cookie banner behavior and consent mode; where each tag is installed (GTM versus plugin versus theme).
- Inputs required: GA4, GTM, Ads read-only access (AUD-001); live site observation.
- Execution method: Export GTM container JSON (read-only export); review GA4 admin and event reports; review Ads conversion settings; observe network requests and tag firing on production with and without consent; map tag installation points.
- Evidence to capture: GTM container export; GA4 configuration screenshots; Ads conversion action list; network request captures for key interactions; consent behavior notes.
- Expected output: Measurement map from user action to event to conversion action, with installation source for each tag and a list of tags to preserve.
- Dependencies: AUD-001, AUD-008, AUD-010.
- Completion criteria: Every active conversion action traced to a site trigger; duplicate or broken tracking noted; installation sources identified.
- Risks / cautions: Interacting with the live site can generate real analytics hits and conversions; prefer preview/debug modes and record test hits. Do not edit containers, publish versions, or change conversions. No ID or secret of accounts beyond those already public in page source.
- Suggested executor: Julián (access, exports); Claude or specialist analytics agent (TBD).

### AUD-012 — Third-party integrations and external security dependencies

- Objective: Identify every external service the site depends on and the migration impact.
- Scope: Embedded scripts and widgets (maps, video, fonts, reviews, chat, booking, payment if any); API calls from the site; webhooks; SMTP/transactional email services used by forms; CDN/font hosts; anti-spam and captcha services; security services (WAF, firewall, reCAPTCHA); API keys that must be replaced rather than copied.
- Inputs required: Crawl and network captures; AUD-008 plugin inventory; AUD-010 form data.
- Execution method: Extract third-party hosts from network captures and source; reconcile against plugin and GTM data; contact-owner and account mapping.
- Evidence to capture: Third-party host table with purpose, owner account, data exchanged, and whether it requires server-side processing.
- Expected output: Integration register classified as keep (client-side), replace, retire, or unknown.
- Dependencies: AUD-008, AUD-010, AUD-011.
- Completion criteria: All third-party hosts are classified and owner-mapped; server-side dependencies enumerated for AUD-014.
- Risks / cautions: Never record secrets. Integrations tied to expiring accounts or unknown owners are high risk.
- Suggested executor: coding agent with Julián (TBD).

### AUD-013 — Performance, Core Web Vitals, and accessibility baseline

- Objective: Record current measured quality so the migration can be shown to meet or exceed it.
- Scope: Lab metrics on representative templates (mobile and desktop); field Core Web Vitals (Search Console, CrUX) where data exists; page weight; render-blocking resources; accessibility automated scan and manual risk review on key templates (contrast, headings, landmarks, alt text, form labels, keyboard/focus, tap targets).
- Inputs required: Representative URL list (AUD-004, AUD-006).
- Execution method: Run standard lab tools on production with documented settings; collect field data from Search Console and public datasets; run automated accessibility scans; record manual observations.
- Evidence to capture: Tool reports with date, device profile, and URL; field data screenshots or exports.
- Expected output: Baseline scorecard per template; list of accessibility risks.
- Dependencies: AUD-004, AUD-006.
- Completion criteria: Each template has lab data; field data recorded or documented as unavailable; accessibility findings listed with severity.
- Risks / cautions: Lab results vary between runs; record multiple runs. Production under cache versus cold may differ. Findings must not become redesign decisions here.
- Suggested executor: coding agent or browser/research agent (TBD).

### AUD-014 — Cloudflare Pages / Workers compatibility and staging state

- Objective: Determine what the target platform can and cannot replicate from the evidence collected, and verify staging safety.
- Scope: Compatibility of each required behavior with Cloudflare Pages / Workers and the Free plan limits (requests, build limits, Worker limits, Functions, form handling, redirects capacity, headers); staging project current state (indexability, robots, headers, `X-Robots-Tag`, noindex controls, access restrictions); Cloudflare account current state (zones, plans, existing Pages projects); deployment pipeline prerequisites.
- Inputs required: Outputs of AUD-003, AUD-004, AUD-008, AUD-010, AUD-012; staging URL; Cloudflare account access.
- Execution method: Read-only review of the staging project and account; documentation review of current Cloudflare limits (verified against current official documentation at execution time); mapping of each requirement to feasible/infeasible/unknown.
- Evidence to capture: Staging response headers, robots and meta robots findings; Cloudflare account state screenshots; official documentation references with dates; compatibility matrix.
- Expected output: Compatibility matrix and staging non-indexation verification with gaps listed. No decisions on architecture.
- Dependencies: AUD-003, AUD-004, AUD-008, AUD-010, AUD-012.
- Completion criteria: Every required behavior is mapped to feasible, infeasible, or unknown; staging indexability state recorded with evidence; Free plan risks listed.
- Risks / cautions: Platform limits change; verify against current documentation at execution time. Do not modify the staging project or Cloudflare settings as part of this package.
- Suggested executor: Claude with coding agent support (TBD).

### AUD-015 — Deployment dependencies, rollback requirements, and post-migration validation requirements

- Objective: Define, from evidence, the requirements (not the implementation) for safe cutover, rollback, and validation.
- Scope: Deployment prerequisites and sequencing constraints; what must be true before any DNS or hosting change is even proposed; rollback requirements (data, DNS TTLs, retained origin, tracking, email); post-migration validation requirements (URL status parity, redirect parity, indexability, sitemap, canonical, structured data, tracking events, conversions, forms, email delivery, performance, Search Console behavior, monitoring window).
- Inputs required: All completed audit packages.
- Execution method: Derive requirement statements from findings; define measurable acceptance criteria for each; identify the evidence each criterion needs.
- Evidence to capture: Requirement-to-finding trace table.
- Expected output: Requirement set for rollback and validation, each traceable to an audit finding.
- Dependencies: AUD-002 through AUD-014.
- Completion criteria: Each high-risk finding has a corresponding rollback or validation requirement; criteria are measurable; unresolved items listed.
- Risks / cautions: Must not become a migration plan. Requirements describe what must be verified, not how it is built.
- Suggested executor: Claude; reviewed by Julián and ChatGPT.

### AUD-016 — Audit consolidation and exit review

- Objective: Consolidate findings and check them against exit criteria for Julián's decision.
- Scope: Findings register; risk register; open questions closure status; assumption verification status; list of decisions now unblocked.
- Inputs required: All completed packages.
- Execution method: Consolidate; reconcile conflicts between packages; present to Julián.
- Evidence to capture: Findings register; assumption status table; exit criteria checklist.
- Expected output: Consolidated audit report and exit criteria checklist for approval.
- Dependencies: AUD-001 through AUD-015.
- Completion criteria: All Audit Exit Criteria satisfied or explicitly waived by Julián in writing in the repository.
- Risks / cautions: Do not mark criteria met on partial evidence.
- Suggested executor: Claude; ChatGPT for repository documentation; Julián for approval.

## Dependencies

| Package | Hard dependencies | Soft dependencies |
|---|---|---|
| AUD-001 | None | None |
| AUD-002 | AUD-001 | None |
| AUD-003 | AUD-001, AUD-002 | None |
| AUD-004 | AUD-003 | AUD-005, AUD-006 |
| AUD-005 | AUD-003 | AUD-008 |
| AUD-006 | AUD-001 | None |
| AUD-007 | AUD-004 | AUD-006 |
| AUD-008 | AUD-001, AUD-003 | None |
| AUD-009 | AUD-004, AUD-008 | None |
| AUD-010 | AUD-008 | AUD-011 |
| AUD-011 | AUD-001, AUD-008, AUD-010 | None |
| AUD-012 | AUD-008, AUD-010, AUD-011 | None |
| AUD-013 | AUD-004, AUD-006 | None |
| AUD-014 | AUD-003, AUD-004, AUD-008, AUD-010, AUD-012 | AUD-013 |
| AUD-015 | AUD-002 to AUD-014 | None |
| AUD-016 | AUD-001 to AUD-015 | None |

AUD-010 and AUD-011 are mutually informative. Execute AUD-010 first for path discovery, then AUD-011, then complete the AUD-010 tracking mapping.

External dependencies: access from Julián to each platform; Julián's approval before every package; ChatGPT to record results in the repository.

## Evidence Standards

1. Every finding cites its evidence (file path in repository, screenshot reference, export name, or command output).
2. Every evidence item records date and time (UTC), source, executor, and method.
3. Raw exports are preferred over screenshots; screenshots are acceptable when no export exists.
4. Facts, observations, and interpretations are labeled separately. Unverified statements are labeled UNVERIFIED.
5. No secrets in the repository: passwords, API keys, tokens, recovery codes, private keys, full payment or personal data. Redact before committing.
6. Personal data of leads, customers, or users is excluded; use counts and structure only.
7. Evidence is reproducible: record tool, version, and settings used.
8. Conflicting evidence is recorded, not resolved by assumption; escalate to Julián.
9. A package is complete only when its outputs are recorded in the repository and its completion criteria are checked against evidence.
10. Output location for evidence and findings is `docs/audits/` pending OQ-02; no other location is used.

## Open Questions

Questions marked BLOCKING prevent execution of the named package.

| ID | Question | Blocks |
|---|---|---|
| OQ-01 | What access does Julián hold today for registrar, DNS host, hosting, WordPress admin, Cloudflare, Search Console, GA4, GTM, Google Ads, and the email provider? | AUD-001 (this is its subject; not a blocker for approval) |
| OQ-02 | Where must audit outputs and evidence be stored, and in what format? `docs/ARTIFACT-CONVENTIONS.md`, `docs/audits/`, and `docs/tracking/` are referenced in `00-START-HERE.md` but do not exist. | BLOCKING for the first package's output recording |
| OQ-03 | `docs/SECRETS-POLICY.md` is referenced but does not exist. Confirm the interim rule: no secrets in the repository (Evidence Standards 5 and 6). | BLOCKING for any package capturing configuration |
| OQ-04 | Is any production crawl authorized, and with what rate limit and time window? | BLOCKING for AUD-003, AUD-004, AUD-005, AUD-013 |
| OQ-05 | May test form submissions and test conversions be performed on production? If yes, what is the test procedure? | AUD-010, AUD-011 |
| OQ-06 | Which email provider serves the domain, and who administers it? | AUD-002 |
| OQ-07 | Who is the current registrar and DNS host, and is Cloudflare already in the chain? | AUD-002, AUD-003 |
| OQ-08 | Which Search Console property type and verification methods exist? | AUD-006 |
| OQ-09 | What date window defines the organic baseline? | AUD-006, AUD-013 |
| OQ-10 | Are there other domains, subdomains, or country/language variants tied to the site? | AUD-002, AUD-004 |
| OQ-11 | Is the Cloudflare account that hosts the staging project the intended long-term account? | AUD-014 |
| OQ-12 | Which executors (coding agent, SEO agent, browser/research agent) are available, and who assigns them? | All packages marked TBD |
| OQ-13 | Is there any contractual, legal, or privacy obligation (consent, data retention) that affects tracking or forms? | AUD-011, AUD-010 |

## Audit Exit Criteria

The audit is complete only when all of the following are satisfied, or explicitly waived in writing in the repository by Julián:

1. AUD-001 through AUD-016 are complete, with outputs recorded in the repository.
2. DNS zone, nameservers, MX, SPF, DKIM, DMARC, and verification records are fully captured and each record is classified; no unknown record is unexamined.
3. The email provider and its dependencies are identified.
4. A complete URL inventory exists with status, redirect, and canonical data, reconciled against sitemap and Search Console sources.
5. A high-value URL and query set is approved by Julián as the preservation priority.
6. Existing redirects are fully documented.
7. All conversion paths are inventoried and traced to tracking events and conversion actions.
8. GA4, GTM, and Google Ads configuration are baselined, including where each tag is installed.
9. WordPress and Elementor functionality is classified as replicate, retire, or unknown, with no unknowns left on high-risk items.
10. Third-party integrations are inventoried and owner-mapped.
11. Performance, Core Web Vitals, and accessibility baselines are recorded.
12. Cloudflare Pages / Workers compatibility is mapped for each required behavior, including Free plan limits verified against current documentation.
13. Staging non-indexation is verified with evidence.
14. Rollback and post-migration validation requirements exist and are traceable to findings.
15. All open questions are answered, deferred by Julián, or converted to documented risks.
16. Julián approves the consolidated audit report.

## Deferred Decisions

These decisions must NOT be made until the named evidence exists. Making them earlier is a governance violation.

| Decision | Requires evidence from |
|---|---|
| Final URL structure, and any URL or slug change | AUD-004, AUD-006, AUD-007 |
| Redirect rules and redirect mapping | AUD-004, AUD-005, AUD-006 |
| Whether any URLs may be dropped or merged | AUD-004, AUD-006, AUD-007 |
| Content migration approach (as-is, rewrite, restructure) | AUD-007, AUD-008, AUD-009 |
| Frontend framework and build approach | AUD-008, AUD-014 |
| Form processing architecture and lead destination | AUD-010, AUD-012, AUD-014 |
| Tracking implementation approach (GTM, direct, server-side) | AUD-010, AUD-011 |
| Consent and privacy implementation | AUD-011, OQ-13 |
| Cloudflare plan selection | AUD-014 |
| Whether Cloudflare becomes DNS host / nameservers move | AUD-002, AUD-003, AUD-014 |
| Email routing or provider changes | AUD-002 |
| Cutover method, timing, and sequence | AUD-015 |
| Rollback mechanism | AUD-015 |
| Whether WordPress is retained, in what form, and for how long | AUD-003, AUD-008, AUD-015 |
| Redesign scope or visual changes | AUD-007, AUD-013 and Julián approval |
| Search Console, GA4, or Ads property changes at cutover | AUD-006, AUD-011 |
| Any production change of any kind | Explicit authorization from Julián |

## Recommended Next Approved Task

**AUD-001 — Access and ownership inventory.**

Rationale: every other package requires read-only access to systems whose existence and ownership are not yet verified. AUD-001 is non-invasive and requires no production interaction. Its output determines which later packages are executable and who can execute them.

Execution is not started. It begins only after Julián approves this plan and records AUD-001 as the Next Approved Item in `docs/PROJECT-STATE.md`.
