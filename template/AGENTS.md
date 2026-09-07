# <PROJECT_NAME> — Coding Agent Guide

## Purpose and invariants

<REPLACE_WITH_3_TO_6_LINES_DESCRIBING_THE_PRODUCT_AND_PRIMARY_RUNTIME>

<REPLACE_WITH_PROJECT_SPECIFIC_DURABLE_INVARIANTS>

Keep project-specific invariants here; procedures have the owners below.

## Ownership

| Change | Owner | Direct consumers / regression evidence |
| --- | --- | --- |
| <product mission/users/outcomes/principles> | `docs/product.md` | product/UX/architecture decisions |
| <public/domain contract> | <owner path> | <consumers and contract tests> |
| <runtime/lifecycle> | <owner path> | <adapters/persistence/lifecycle tests> |
| <persistence> | <owner path> | <migrations/recovery tests> |
| <UI/transport> | <owner path> | <domain contract/journey tests> |
| <product experience> | `design/ux-contract.json` | <components/tokens/journeys> |

Follow applicable scoped `AGENTS.md`. Extend the owner before parallel state/policy; inspect material consumers when shared boundaries change.

## Read by task

| Task | Local procedure / source |
| --- | --- |
| README/copy/metadata | Affected source/links; `docs/README.md` if ownership is unclear. |
| Product capability/behavior/strategy | `.engineering/product.json`, `docs/product.md`, `skills/shape-product-change/SKILL.md`. |
| Behavior/bug/contract | `skills/structured-change/SKILL.md`, `skills/validate-change/SKILL.md`, relevant commands. |
| Material product UI | Above + `skills/design-product-experience/SKILL.md` and design contracts. |
| Integration/release | `skills/preflight-change/SKILL.md`, commands, `.engineering/e2e.json`. |
| Missing remote gates | `skills/remote-preflight/SKILL.md`. |
| Persistent coordination/completion | `skills/plan-workstream/SKILL.md` + active plan / `skills/finalize-workstream/SKILL.md`. |
| Reference milestone | `skills/review-reference-quality/SKILL.md`. |

Read architecture/features/ADRs only for concrete questions. Ordinary work uses this repository's adopted baseline; upstream instructions apply only to migrations.

## Product and delivery boundaries

Product depth, delivery stage and validation depth are independent.

- **PRODUCT_NONE/LOCAL**: keep product reasoning local; preserve settled intent and prove the outcome.
- **PRODUCT_FEATURE/STRATEGIC**: before substantial implementation establish user/problem/outcome, material value/usability/feasibility/viability risks, assumptions, success evidence and non-goals. Discovery may narrow, change or reject the requested solution.
- **ITERATION**: fast owner-local feedback; no exact-head/full-diff/docs/publication ceremony per edit.
- **INTEGRATION**: coherent outcome, current affected docs, exact candidate/base, required automated gates and affected critical E2E. Material UI/UX journeys default to `FULL_MEDIA`; residual real-environment evidence is `DEFERRED_TO_RELEASE`.
- **RELEASE**: `FULL` release evidence plus applicable blocking real-environment confirmation.

`SHIPPED` proves delivery, not product impact. Add post-release learning only when material real-use uncertainty remains; telemetry is not mandatory.

Select concrete risks/gates; unknown executable scope fails safe stronger. Reuse only equivalent successful evidence. Missing local tools do not make the user the runner. Emulator proof never establishes physical behavior.

## Context and completion

Use scoped search/bounded output; refresh source identity and relevant changes on resume. Notes are pointers, not proof.

`.engineering/documentation-policy.json` owns context routes/budgets. Use `python3 scripts/verify_agent_context.py --route product --format json` for material product shaping or `--route bug` for implementation; add `--path`/`--workstream` when relevant. Routes are accounting aids, not validation selectors.

Before integration, follow preflight and update affected canonical docs. `docs/product.md` owns durable mission/users/problems/outcomes/principles; `docs/current-state.md` owns integrated/blocked/next truth. Transfer durable knowledge and deferred release obligations before deleting completed plans.

Surface material ambiguity/invariant conflicts. Never suppress legitimate tests, expose private state, claim missing evidence passed or silently downgrade gates.
