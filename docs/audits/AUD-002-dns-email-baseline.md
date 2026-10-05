# AUD-002 — DNS, Nameserver, and Email-Authentication Baseline

Status: COMPLETED WITH DEFERMENT
Date: 2026-10-04
Executor: Julián Cely / ChatGPT

## Objective

Capture the current DNS and email-authentication baseline for `mariachisenciudaddemexico.com` without changing production configuration.

## Scope Completed

The following were verified through public DNS queries and Zoho Mail Admin:

- delegated nameservers;
- apex A record;
- `www` CNAME;
- AAAA presence/absence;
- MX records;
- SPF;
- DKIM selector and verification state;
- DMARC;
- CAA;
- Zoho domain ownership state.

## Authoritative DNS Provider

Public delegation confirms Wix DNS:

- `ns14.wixdns.net`
- `ns15.wixdns.net`

Observed NS TTL: `86400`.

The SOA response also identifies:

- primary server: `ns14.wixdns.net`
- administrative domain: `support.wix.com`
- serial observed: `2021092919`

Evidence:

- `docs/audits/evidence/AUD-002/public-ns.txt`
- `docs/audits/evidence/AUD-002/public-www.txt`

## Web DNS

### Apex

`mariachisenciudaddemexico.com`

- Type: A
- Value: `74.208.94.17`
- TTL observed: `1800`

Evidence:

`docs/audits/evidence/AUD-002/public-a-root.txt`

### WWW

`www.mariachisenciudaddemexico.com`

- Type: CNAME
- Target: `mariachisenciudaddemexico.com`
- TTL observed: `3600`

Evidence:

`docs/audits/evidence/AUD-002/public-www.txt`

### IPv6

No AAAA answer was observed for the apex during the recorded query.

Evidence:

`docs/audits/evidence/AUD-002/public-aaaa-root.txt`

## Email Provider

Provider: Zoho Mail.

Public MX records and Zoho Admin both confirm Zoho as the active mail provider.

## MX Baseline

Public DNS observed:

| Priority | Host |
|---:|---|
| 10 | `mx.zoho.com` |
| 20 | `mx2.zoho.com` |
| 30 | `mx3.zoho.com` |

Observed TTL: `3600`.

Zoho Admin currently expects:

| Priority | Host |
|---:|---|
| 10 | `mx.zoho.com` |
| 20 | `mx2.zoho.com` |
| 50 | `mx3.zoho.com` |

### Finding AUD-002-F01 — MX priority mismatch

The tertiary MX hostname matches between public DNS and Zoho, but its priority differs:

- Public DNS: `30`
- Zoho Admin expected value: `50`

No DNS change was made.

Evidence:

- `docs/audits/evidence/AUD-002/public-mx.txt`
- `docs/audits/evidence/AUD-002/zoho-mx-comparison.txt`

## SPF Baseline

Public SPF:

`v=spf1 include:zoho.com ~all`

Observed TTL: `3600`.

Zoho Admin currently reports SPF as configured successfully and its expected value matches the public DNS record.

### Finding AUD-002-F02 — Previous SPF warning no longer reproduced

A prior Zoho notification reported an SPF discrepancy. During AUD-002:

- public DNS returned the Zoho SPF record;
- Zoho Admin reported SPF configured successfully;
- both values matched.

The previous warning is therefore retained as historical context, not treated as a currently reproduced failure.

Evidence:

- `docs/audits/evidence/AUD-002/public-txt-root.txt`
- `docs/audits/evidence/AUD-002/zoho-spf-status.txt`

## DKIM Baseline

Zoho Admin identifies the active DKIM selector as:

`zmail`

Public DNS contains:

`zmail._domainkey.mariachisenciudaddemexico.com`

Zoho reports:

`DKIM selector is successfully verified`

Observed TTL: `3600`.

The public DKIM key itself is retained only in the raw DNS evidence and is not duplicated in this report.

Evidence:

- `docs/audits/evidence/AUD-002/public-dkim-zmail.txt`
- `docs/audits/evidence/AUD-002/zoho-dkim-status.txt`

## DMARC Baseline

Public query:

`_dmarc.mariachisenciudaddemexico.com`

Result:

`NXDOMAIN`

Zoho Admin also shows no active DMARC configuration.

### Finding AUD-002-F03 — DMARC not configured

No DMARC record is currently published at the standard `_dmarc` host.

This is a baseline observation only. AUD-002 does not authorize creating or changing DMARC.

Evidence:

- `docs/audits/evidence/AUD-002/public-dmarc.txt`
- `docs/audits/evidence/AUD-002/zoho-dmarc-status.txt`

## CAA Baseline

A DNS-over-HTTPS query for apex CAA returned a successful DNS response with no CAA Answer section.

### Finding AUD-002-F04 — No apex CAA record observed

No CAA record was observed at the domain apex during the recorded query.

Evidence:

`docs/audits/evidence/AUD-002/public-caa.txt`

## TXT and Verification Records

The public apex TXT query returned:

`v=spf1 include:zoho.com ~all`

No additional apex TXT verification records were observed in that query.

`www` is a CNAME to the apex and did not expose a separate TXT record.

Known non-DNS verification dependencies from AUD-001 include Search Console verification through:

- HTML tag;
- Google Tag Manager.

Zoho confirms domain ownership as verified, but the General view inspected during AUD-002 did not expose the original verification method.

Personal verifier information and unique verification codes were intentionally excluded.

Evidence:

- `docs/audits/evidence/AUD-002/public-txt-root.txt`
- `docs/audits/evidence/AUD-002/public-txt-www.txt`
- `docs/audits/evidence/AUD-002/zoho-domain-ownership.txt`

## Current Record Classification

| Record / Host | Classification | Criticality |
|---|---|---|
| NS `ns14.wixdns.net`, `ns15.wixdns.net` | Authoritative DNS | Critical |
| Apex A `74.208.94.17` | Web production | Critical |
| `www` CNAME → apex | Web production | Critical |
| MX `mx.zoho.com` | Email | Critical |
| MX `mx2.zoho.com` | Email | Critical |
| MX `mx3.zoho.com` | Email | Critical |
| SPF `include:zoho.com` | Email authentication | Critical |
| DKIM `zmail._domainkey` | Email authentication | Critical |
| `_dmarc` | Not currently published | Email authentication gap |
| Apex CAA | Not currently observed | Other / TLS policy |
| Search Console HTML tag verification | Site verification | Critical to preserve |
| Search Console GTM verification | Site verification | Critical to preserve |

## Evidence Limitation / Blocker

### DEFERRED AUD-002-D01 — No authoritative Wix zone export or DNS-panel view

The client controls the Wix registrar / authoritative DNS account.

Julián does not currently have operational access and client involvement has intentionally been deferred until migration-related work requires it.

Therefore AUD-002 currently lacks:

- complete Wix DNS-zone export or equivalent panel inventory;
- registrar-side nameserver screenshot;
- authoritative confirmation of all records and TTLs visible in the Wix panel;
- confirmation of records that may exist but are not discoverable through the targeted public queries performed so far;
- authoritative reconciliation of every DNS record to a service.

Public DNS establishes the currently resolvable records queried during this audit, but it cannot satisfy the audit-plan requirement that every record in the zone be accounted for.

## Completion Criteria Review

- Delegated nameservers identified: YES
- Email provider identified: YES
- MX publicly captured: YES
- SPF captured and reconciled with Zoho: YES
- DKIM selector identified and reconciled with Zoho: YES
- DMARC state captured: YES
- CAA state captured: YES
- Web apex / www records captured: YES
- Public and Zoho views reconciled where accessible: YES
- Full authoritative DNS zone captured: NO
- Registrar nameserver view captured: NO
- Every record in authoritative zone accounted for or flagged: NO

## Package Status

`AUD-002` is **COMPLETED WITH DEFERMENT** by Julián's explicit decision.

The public and Zoho baseline is accepted as the current audit baseline. Authoritative Wix zone/export and registrar-side nameserver evidence are explicitly deferred until migration-related work and must be captured before any production DNS or nameserver change.

No DNS, email, registrar, nameserver, or production configuration was changed during this work.
