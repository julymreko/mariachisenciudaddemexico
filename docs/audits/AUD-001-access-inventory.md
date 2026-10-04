# AUD-001 — Access and Ownership Inventory

Status: COMPLETED
Date: 2026-10-03
Executor: Julián Cely / ChatGPT

## Objective

Establish which systems exist, who controls them, and what access is currently available before deeper audit work begins.

## Access Matrix

| System | Provider / Platform | Exists | Operational owner | Julián access | Verified access level | Notes |
|---|---|---:|---|---|---|---|
| Domain registrar | Wix.com Ltd. | Yes | Client | No current operational access | Client-managed | Client involvement deferred until migration phase. |
| Authoritative DNS | Wix DNS | Yes | Client | No current operational access | Client-managed | Nameservers observed: `ns14.wixdns.net`, `ns15.wixdns.net`. Full authoritative zone access not currently available. |
| Production hosting | IONOS | Yes | Julián | Yes | VPS: SSH and console | VPS hosts current production WordPress. |
| Server control panel | Plesk | Unverified | Julián | Unknown | Unverified | Plesk is believed to exist, possibly without an active license. Must be verified in AUD-003. |
| WordPress production | WordPress + Elementor Pro | Yes | Julián | Yes | Administrative access | Read-only inspection only unless separately authorized. |
| Cloudflare account | Cloudflare | Yes | Julián | Yes | Operational access | Access to Workers & Pages project settings confirmed. |
| Cloudflare Pages staging | `mariachisenciudaddemexico.pages.dev` | Yes | Julián | Yes | Project settings access | GitHub-connected project using branch `main`. No configuration changes authorized during audit. |
| Google Search Console | Google | Yes | Julián | Yes | Verified Owner | URL-prefix property for `https://www.mariachisenciudaddemexico.com/`. Verification currently includes HTML tag and Google Tag Manager. |
| Google Analytics 4 | Google | Yes | Julián | Yes | Administrator | Production property accessible. |
| Google Tag Manager | Google | Yes | Julián | Yes | Publication permission | Two candidate containers associated with the domain. Active production container is not yet verified. |
| Google Ads | Google | Yes | Client | No | Client-managed | Access/export will be requested only when AUD-011 requires it. |
| Email provider | Zoho Mail | Yes | Julián | Yes | Admin Console access | Zoho has reported an SPF discrepancy. No DNS correction is authorized yet; carry forward to AUD-002. |
| Other third-party admin services | Unknown / none currently known | Unknown | TBD | TBD | TBD | Julián does not currently recall additional services. Later audits may discover more. |

## GTM Containers Requiring Later Verification

Two containers associated with the production domain are accessible:

- `Mariachi Juvenil La Estrella de Jalisco` — `GTM-KG4JF7D`
- `mariachisenciudaddemexico.com` — `GTM-PH47T78M`

Which container is actually active on production is intentionally deferred to AUD-011.

## Access Gaps

### GAP-001 — Wix registrar / authoritative DNS

The registrar and DNS configuration are controlled by the client.

Julián does not currently have operational access and does not intend to involve the client until migration-related work requires it.

Impact:

- AUD-002 can perform public DNS observation.
- A complete authoritative DNS-zone baseline cannot be considered verified until client-provided panel access, export, or equivalent evidence becomes available.

### GAP-002 — Google Ads

Google Ads is managed by the client and Julián does not currently have account access.

Impact:

- AUD-011 will require either read-only access or approved exports from the client before conversion configuration can be fully baselined.

## Known Risk Observation for Later Audit

Zoho Mail has reported that SPF for `mariachisenciudaddemexico.com` is disabled or inconsistent because of discrepancies in the DNS SPF record.

This is not investigated or corrected in AUD-001.

Carry forward to:

`AUD-002 — DNS, nameserver, and email-authentication baseline`

No DNS changes are authorized.

## Sensitive Information Handling

The following information observed during AUD-001 is intentionally excluded from this repository artifact:

- personal WHOIS contact details;
- customer or account email addresses;
- IONOS contract/account identifiers;
- credentials or authentication material.

This follows `docs/SECRETS-POLICY.md`.

## Completion Criteria Review

- Registrar: identified.
- DNS host: identified.
- Hosting: identified and accessible.
- WordPress: identified and accessible.
- Cloudflare: identified and accessible.
- Search Console: identified and accessible.
- GA4: identified and accessible.
- GTM: identified and accessible; active container deferred to AUD-011.
- Google Ads: identified; access gap documented.
- Email provider: identified and accessible.
- Staging project: identified and accessible.
- Known access gaps: documented.

AUD-001 completion criteria are satisfied.

## Follow-up

AUD-002 requires review of DNS, nameservers, MX, SPF, DKIM, DMARC, verification records, and email dependencies.

The lack of current Wix DNS-panel access must remain explicitly documented during AUD-002 and must not be replaced by assumptions based only on public DNS results.
