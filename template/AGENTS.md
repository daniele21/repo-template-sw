# <PROJECT_NAME> — Coding Agent Guide

## Purpose and invariants

<REPLACE_WITH_3_TO_6_LINES_DESCRIBING_THE_PRODUCT_AND_PRIMARY_RUNTIME>

<REPLACE_WITH_PROJECT_SPECIFIC_DURABLE_INVARIANTS>

Keep project-specific invariants here; procedures have the owners below.

## Ownership

| Change | Owner | Direct consumers / regression evidence |
| --- | --- | --- |
| <public/domain contract> | <owner path> | <consumers and contract tests> |
| <runtime/lifecycle> | <owner path> | <adapters/persistence/lifecycle tests> |
| <persistence> | <owner path> | <migrations/recovery tests> |
| <UI/transport> | <owner path> | <domain contract/journey tests> |
| <product experience> | `design/ux-contract.json` | <components/tokens/journeys> |

Follow applicable scoped `AGENTS.md` along affected paths. Extend the owner before adding parallel state or policy; inspect material consumers when a shared boundary changes.

## Read by task

Read this guide and applicable scoped guides, then the route needed now:

| Task | Local procedure / source |
| --- | --- |
| Pure README/copy/metadata | Affected source/links and native docs checks; `docs/README.md` if ownership is unclear. |
| Meaningful behavior/bug/contract | `skills/structured-change/SKILL.md`, `skills/validate-change/SKILL.md`, relevant `.engineering/commands.json` fields. |
| Material product UI | Above plus `skills/design-product-experience/SKILL.md` and relevant design contracts. Dormant in headless projects. |
| Integration/release readiness | `skills/preflight-change/SKILL.md`, commands, and `.engineering/e2e.json` for affected journey/environment claims. |
| Missing required remote gates | `skills/remote-preflight/SKILL.md` at readiness; focused remote commands during iteration only when needed to test a hypothesis. |
| Persistent coordination / completion | `skills/plan-workstream/SKILL.md` + active plan / `skills/finalize-workstream/SKILL.md`. |
| Reference-quality milestone | `skills/review-reference-quality/SKILL.md`. |

Read architecture/features/ADRs to answer concrete questions. Ordinary development uses this repository's adopted baseline; upstream adoption/update instructions apply only to explicit migrations.

## Delivery boundaries

- **ITERATION**: owner-local feedback; no exact-head/full-diff/docs/publication ceremony after each edit.
- **INTEGRATION**: coherent observable outcome, current affected docs, exact candidate/base evidence, required automated gates and affected critical E2E. Material UI/UX journeys require screenshots and continuous video (`FULL_MEDIA`). Residual physical confirmation is `DEFERRED_TO_RELEASE`.
- **RELEASE**: `FULL` release-grade evidence plus all applicable blocking real-environment confirmations.

Stage and validation depth are independent. Select concrete risks/gates; unknown executable scope fails safe stronger. Reuse only provably equivalent successful evidence. Missing local tools do not make the user the runner for automatable gates. Emulator proof never establishes physical behavior.

## Context and completion

Use scoped search and bounded output. Read generated/dependency artifacts only when needed. Reuse unchanged sources within a session; refresh identity/relevant changes on resume. Notes are pointers, not current-source proof.

`.engineering/documentation-policy.json` owns context budgets/routes. `python3 scripts/verify_agent_context.py --route bug --format json` reports file/cost estimates; add `--path <affected-path>` or `--workstream <active-plan>`. Routes are accounting examples, not validation selectors or permission to omit relevant instructions/code.

Before integration follow preflight and update affected canonical docs. `docs/current-state.md` owns integrated/blocked/next truth. Transfer durable knowledge and deferred release obligations before deleting completed plans. Keep resources bounded and clean up owned temporary state.

Surface unresolved material ambiguity or invariant conflicts. Never suppress legitimate tests, expose private state in logs/media, claim missing evidence passed or silently downgrade gates. Equivalent implementation choices remain autonomous.
