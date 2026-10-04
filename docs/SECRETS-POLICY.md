# SECRETS POLICY

> **Mandatory rule:** No secrets, credentials, tokens, private keys, recovery codes, sensitive configuration values, or personal lead/customer data may be stored in the repository or shared with AI agents unless explicitly classified as safe public information.

## Purpose

Prevent credentials, secrets, sensitive configuration, and personal data from entering the Git repository or project documentation.

## Never Commit

The following must never be committed to the repository:

- passwords
- API keys
- access tokens
- refresh tokens
- OAuth client secrets
- private keys
- SSH private keys
- recovery codes
- session cookies
- database credentials
- SMTP passwords
- WordPress application passwords
- Cloudflare API tokens
- Google API credentials
- Google Ads authentication secrets
- registrar or DNS account credentials
- email account credentials
- payment information
- personal lead or customer data

## Allowed References

Documentation may record:

- provider or service name
- account owner
- account identifier when non-sensitive
- public measurement IDs
- public tag IDs already exposed by the website
- property names
- container names
- verification methods
- whether access exists
- access level
- where a secret is stored, without recording the secret itself

## Evidence Handling

Before committing any export, screenshot, configuration file, log, or command output:

1. Review it for secrets and personal data.
2. Redact sensitive values.
3. Remove unnecessary personal information.
4. Store only the minimum evidence required for the task.

If safe redaction cannot be guaranteed, do not commit the artifact.

## Chat and AI Agents

Secrets must not be pasted into AI conversations.

Agents must never request passwords, tokens, private keys, recovery codes, or equivalent credentials.

If authentication is required, Julián performs it directly in the relevant service or approved connector.

## Local Files

Sensitive files may exist locally only when operationally necessary.

They must:

- remain outside Git tracking;
- not be copied into documentation;
- not be included in screenshots or logs committed as evidence.

## Incident Rule

If a secret is accidentally exposed in Git or an AI conversation:

1. Stop the current task.
2. Inform Julián immediately.
3. Treat the exposed credential as compromised.
4. Revoke or rotate it before continuing.
5. Remove it from project artifacts where practical.

Deleting a secret from the latest Git commit does not make it safe if it existed in repository history.

## Default Rule

When uncertain whether information is sensitive, do not commit or share it until Julián confirms it is safe.
