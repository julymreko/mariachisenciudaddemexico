# CONTEXT PROTOCOL

## Purpose

Define the minimum context every agent must load before executing project work.

## Mandatory Read Order

Before executing any project task, the agent must read, in this order:

1. `docs/00-START-HERE.md`
2. `docs/PROJECT-STATE.md`
3. The task-specific authoritative document, if one exists
4. Any directly related decision, finding, audit, or handoff document

## Source of Truth

The Git repository is the authoritative project memory.

AI conversations are temporary coordination channels and are not authoritative unless the relevant decision, finding, task result, or state change is recorded in the repository.

## Missing Context Rule

If a referenced mandatory document does not exist, the agent must stop and ask Julián for clarification or creation of the missing document.

The agent must not infer or invent missing project state.

## Conflict Rule

If two documents conflict:

1. `docs/00-START-HERE.md` governs agent behavior.
2. `docs/PROJECT-STATE.md` governs current phase, active item, and next approved item.
3. Explicit accepted decisions govern architecture or implementation choices.
4. If conflict remains unresolved, stop and ask Julián.

## Task Execution Rule

Agents must only execute the currently approved task or work package.

Do not continue automatically into subsequent tasks.

## Documentation Update Rule

After completing a meaningful task, the result must be recorded in the appropriate authoritative project document before the task is considered complete.

## Human Execution Rule

Any instruction requiring Julián to act manually must follow the sequential atomic format defined in `docs/00-START-HERE.md`.

## Production Protection

Read-only inspection is allowed when explicitly part of an approved task.

Any production modification requires explicit authorization from Julián.
