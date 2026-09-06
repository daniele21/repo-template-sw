# <WORKSTREAM_TITLE>

Status: active
Owner: <DOMAIN_OR_REPOSITORY>
Read when: implementing or coordinating <WORKSTREAM_SCOPE>

## Goal

<ONE_CLEAR_OUTCOME>

## Non-goals

- <EXPLICITLY_EXCLUDED_SCOPE>

## Invariants

- <INVARIANT_THAT_MUST_REMAIN_TRUE>

## Work graph

| ID | Work | Owns/writes | Depends on | Parallel | State |
| --- | --- | --- | --- | --- | --- |
| WS-1 | <bounded slice> | <paths/boundary> | — | yes | READY |
| WS-2 | <bounded slice> | <paths/boundary> | WS-1 | no | BLOCKED |

Allowed states: `READY`, `ACTIVE`, `BLOCKED`, `DONE`.

Parallel work must have explicit non-conflicting ownership/write boundaries or a defined integration point.

## Current executable slice

`WS-1`

Acceptance:

- <observable acceptance criterion>

Validation:

- `<targeted command or evidence>`

## Resume checkpoint

<!-- Optional for multi-session work. Replace these pointers at a meaningful finding/handoff, not after every edit. -->

- Source: <repository/branch/head and target/base; dirty/uncommitted work if any>
- Confirmed: <facts with source/run/artifact references>
- Excluded: <hypotheses and the observations that ruled them out>
- Unresolved: <remaining uncertainty; distinguish it from facts>
- Next: <one concrete experiment or implementation action>

Recheck current revisions, changed files and evidence validity before acting. Notes do not override code/contracts or prove a gate passed. Retain no private payloads; link bounded evidence.

## Integration points

- <contract/merge point between parallel slices>

## Durable documentation destinations

- `docs/architecture.md`: <only if architecture/ownership changes>
- `docs/features/<feature>.md`: <only durable current behavior>
- `docs/adr/<adr>.md`: <only if a material durable decision is made>
- tests/contracts: <executable truth>

## Completion

The workstream is complete only when applicable code, integration, failure/resource behavior, validation/evidence and durable docs agree. Then update `docs/current-state.md` and delete this file by default.
