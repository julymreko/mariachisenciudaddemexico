# ARTIFACT CONVENTIONS

## Purpose

Define where project documentation, audit outputs, and evidence are stored and how they are named.

## Authoritative Documentation

Governance and current-state documents live directly under:

`docs/`

Audit planning lives in:

`docs/AUDIT-PLAN.md`

## Audit Outputs

Outputs produced by audit work packages must be stored under:

`docs/audits/`

Each audit work package should use a file named:

`AUD-###-short-description.md`

Example:

`docs/audits/AUD-001-access-inventory.md`

## Evidence

Raw evidence should only be committed when it is safe, necessary, and contains no secrets or personal data.

Evidence related to an audit package should be stored under:

`docs/audits/evidence/AUD-###/`

Evidence filenames should be descriptive and include the capture date when useful.

Example:

`2026-10-03-dns-zone-export.txt`

## Tracking

Cross-package tracking artifacts may be stored under:

`docs/tracking/`

Create tracking files only when an approved task requires them.

## Naming Rules

- Use uppercase stable IDs for audit packages: `AUD-001`.
- Use lowercase descriptive filenames after the ID.
- Use ISO dates: `YYYY-MM-DD`.
- Do not use names such as `final`, `final-v2`, `new`, or `latest`.
- Git history provides versioning.

## Content Rules

- Facts must be distinguishable from assumptions and interpretations.
- Evidence must be referenced from the audit output that produced it.
- Secrets, credentials, tokens, private keys, recovery codes, and personal lead/customer data must never be committed.
- Unknown information must remain explicitly marked as unknown.
- Do not create empty folders or placeholder files unless an approved task requires them.

## Change Control

Artifacts are created only when required by an approved task.

Do not create additional documentation structures speculatively.
