# MARIACHISENCIUDADDEMEXICO.COM — START HERE

## Project Authority

Product Manager: Julián Cely

The Product Manager is the final authority for product, scope, architecture, and unresolved decisions.

## Mandatory Agent Rules

1. All AI-to-AI instructions must be written in technical English.

2. No agent may invent, hallucinate, complete missing information, or make an undefined decision.

3. If information is vague, incomplete, contradictory, or undefined, execution must stop and clarification must be requested from Julián Cely.

4. Token economy is mandatory. Every word must serve the task. Avoid decorative explanation, redundant context, speculative alternatives, and unnecessary background.

5. Julián Cely will request additional detail when needed.

## Sequential Execution Protocol

All human-executed instructions must be sequential and atomic.

For every instruction addressed to Julián Cely, the agent must provide exactly:

- Action — one concrete action to perform.
- Location — exactly where the action must be executed.
- Expected Result — the observable output or state that confirms success.

The agent must stop after the instruction and wait for confirmation.

No subsequent execution step may be provided in advance.

If the observed result differs from the expected result, execution must stop and the discrepancy must be diagnosed before proceeding.

No agent may assume that a previous instruction succeeded without explicit confirmation.

No unverified success may propagate to a subsequent step.

## Source of Truth

The Git repository is the authoritative project memory.

AI conversations are not a source of truth unless their approved decisions are recorded in the repository.

## Mandatory Governance Index

Before executing project work, every agent must follow the context-loading rules defined in:

docs/CONTEXT-PROTOCOL.md

Authoritative governance documents:

- docs/PROJECT-STATE.md
- docs/ARCHITECTURE.md
- docs/ARTIFACT-CONVENTIONS.md
- docs/CONTEXT-PROTOCOL.md
- docs/TESTING-PROTOCOL.md
- docs/SECRETS-POLICY.md
- docs/PRE-DEPLOYMENT-AUDIT.md
- docs/decisions/
- docs/phases/
- docs/templates/
- docs/tracking/
- docs/audits/

Artifact locations and naming conventions are defined in:

docs/ARTIFACT-CONVENTIONS.md
## Current Project State

The current verified project phase, status, active working item, and next approved item are defined exclusively in:

docs/PROJECT-STATE.md

Do not duplicate current phase or status values in this document.

Agents must read docs/PROJECT-STATE.md before executing project work.