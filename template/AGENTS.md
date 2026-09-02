# <PROJECT_NAME> — Coding Agent Guide

This is the repository-wide routing layer for coding agents. It owns durable invariants, delivery/validation routing and ownership discovery, not project status or detailed architecture.

## Read only what the task requires

Always read this guide. Then read only:

1. the closest scoped `AGENTS.md` for the target subtree, if present;
2. the canonical architecture/feature/workstream source required by the task;
3. `.engineering/commands.json` for delivery stage, setup/dev/test/build/runtime/cleanup, execution capability and integration/release readiness;
4. `.engineering/e2e.json` only for complete-workflow, target device/platform/browser/runtime, E2E fidelity or UI-evidence questions;
5. `skills/validate-change/SKILL.md` while iterating, `skills/preflight-change/SKILL.md` when the slice becomes integration/release-ready, and `skills/remote-preflight/SKILL.md` only when required deterministic gates cannot run locally;
6. when `product-ui` is adopted and user-facing semantics change, `design/ux-contract.json`, `design/brand-kit.json` and `skills/design-product-experience/SKILL.md`;
7. the owning implementation, direct consumers and nearby tests.

Do not load every plan or all documentation for a local change.

## Repository purpose

<REPLACE_WITH_3_TO_6_LINES_DESCRIBING_THE_PRODUCT_AND_PRIMARY_RUNTIME>

## Non-negotiable invariants

<REPLACE_WITH_PROJECT_SPECIFIC_DURABLE_INVARIANTS>

Keep this list short. Do not copy generic rules already enforced by the standard, Skills or CI unless this project needs a specialization.

## Ownership and routing

| Change | Start here | Inspect next |
| --- | --- | --- |
| <public/domain contract> | <owner path> | <direct consumers/tests> |
| <runtime/lifecycle> | <owner path> | <adapters/persistence/tests> |
| <persistence/data lifecycle> | <owner path> | <migrations/consumers/tests> |
| <UI/transport adapter> | <owner path> | <owning domain contract/tests> |
| <product experience / design system, if applicable> | `design/ux-contract.json` | `skills/design-product-experience/SKILL.md` + <canonical design/component source> |

Add scoped guides only where a subtree has meaningful local invariants, hazards, ownership or validation commands.

## Delivery stages

`.engineering/commands.json` separates delivery stage from validation depth.

### `ITERATION`

Default while implementation is changing.

Goal: falsify the current edit quickly with the cheapest useful formatter/static/compile/unit/direct-contract gates. Exact-head publication evidence, complete diff review, durable-documentation freshness, remote preflight and release-grade E2E are **not** default iteration requirements.

A draft/collaboration PR may exist without being integration-ready.

### `INTEGRATION`

Use when a coherent vertical slice now provides an observable user/system outcome and is ready to converge into the shared integration branch or be marked ready for merge/review.

Now refresh base/head, inspect the complete diff, make affected durable docs current, select risk gates, execute/rout deterministic evidence and add the smallest necessary critical E2E.

### `RELEASE`

Use for stable-branch promotion/release candidates/reference checkpoints. Expect `FULL` validation plus release-critical artifact/E2E and residual environment evidence.

## Project operating commands

`.engineering/commands.json` owns command/development-velocity/execution routing. `.engineering/e2e.json` owns target environments, execution environments, fidelity gaps and critical journeys.

Use declared intents rather than inventing parallel paths:

- `check` — broad cheap validation;
- `test` — unit/integration/contract behavior;
- `e2e` — complete critical workflow when lower-level tests are insufficient;
- `build` — runnable/build output;
- `smoke` — minimum built/runtime viability;
- `package` — distributable output when relevant;
- `stop`/`clean` — project-owned runtime/generated state.

Do not treat `e2e` and `smoke` as synonyms. Keep E2E small and critical.

## Validation routing

Validation selection proceeds in this order:

```text
changed outcome
-> risk dimensions
-> required gates
-> LEAN | SCOPED | STRONG | FULL summary
-> executor: AGENT_LOCAL | REMOTE_AUTOMATED | REAL_ENVIRONMENT
```

Do not escalate because a broad feature/domain label sounds risky. Escalate because the changed invariant requires a stronger gate.

`FULL` is expected for release/promotion and exceptional for ordinary feature work.

When a deterministic gate cannot run locally, route it to repository automation. Do not turn the user into the runner.

Before triggering expensive remote validation at integration/release, reuse successful evidence that still matches exact head, material target/base relationship, required gates/profile and E2E environment/evidence mode. A replacement PR or draft/ready metadata change alone does not invalidate equivalent source evidence.

## E2E routing

Executor and environment fidelity are independent. `AGENT_LOCAL`, `REMOTE_AUTOMATED` and `REAL_ENVIRONMENT` say who/where executes a gate; `.engineering/e2e.json` says how representative the environment is.

Use the cheapest automated E2E environment that proves the claim and escalate only when a material target dimension requires it.

For journeys crossing a UI, choose evidence mode from the actual claim:

- `ASSERTIONS` — UI is incidental; deterministic system behavior is the changed truth;
- `SCREENSHOTS` — stable visible layout/hierarchy/copy/state/recovery/adaptive semantics changed;
- `FULL_MEDIA` — motion, timing/progression, navigation/transition sequence, lifecycle visibility, gesture continuity or release/product acceptance requires observing the journey over time.

UI presence alone does not force video. Missing evidence required by the selected mode means `E2E_EVIDENCE_INCOMPLETE`.

Final target testing should primarily confirm residual fidelity gaps.

## Product experience routing

When `product-ui` is adopted, meaningful UX/UI work follows at proportional depth:

```text
user outcome
-> task model
-> information architecture / critical journey
-> information + action hierarchy
-> progressive disclosure / defaults
-> interactions / states / feedback / recovery
-> adaptive / platform behavior
-> accessibility
-> design system / components
-> motion
-> visual polish / graphics
-> validation
```

- structural UX — use the full sequence;
- interaction — start from the owning task/journey and affected layers;
- visual-only — preserve settled flow/semantics and start from the canonical design-system/brand owner.

Do not expose implementation complexity or use polish to compensate for unresolved task flow/hierarchy/feedback.

## Core change workflow

1. Confirm owning boundary and smallest coherent outcome.
2. Resolve material ambiguity from canonical repository evidence; ask only when meaningful product/contract alternatives remain.
3. Use `plan-workstream` only when persistent dependency/parallel coordination is useful.
4. Prefer an observable vertical outcome. Treat technical layers as subtasks unless independently valuable/mergeable/reviewable.
5. Parallel branches may execute independently but should converge early onto a coherent feature/integration branch; stacked publication is exceptional.
6. Use `structured-change` before/after meaningful behavior changes.
7. Use `validate-change` in `ITERATION` and keep feedback narrow.
8. Diagnose the owning invariant before patching failures.
9. When the slice has an observable outcome, move to `INTEGRATION`.
10. At integration, update affected durable docs, refresh base/head, review the complete diff and select required risk gates.
11. Use E2E only when lower-level evidence cannot prove the complete affected outcome; select environment/fidelity/evidence mode proportionally.
12. Use `preflight-change` for exact-head integration/release readiness.
13. Reuse equivalent successful validation evidence, then route only missing `REMOTE_AUTOMATED` gates through `remote-preflight`.
14. Finalize completed workstreams and delete plans by default after durable truth is transferred.

## Documentation lifecycle

`docs/README.md` owns documentation routing.

- README identity — stable mission/audience/outcome;
- README usage — setup/run/use/configuration/public examples;
- `docs/architecture.md` — current architecture/ownership;
- `docs/features/` — durable non-obvious feature behavior;
- `docs/adr/` — accepted durable decisions;
- `docs/current-state.md` — short **integrated/blocked/next** repository truth, not minute-by-minute agent activity;
- `docs/workstreams/` — active bounded plans only;
- `design/` — UX/brand contracts when `product-ui` is adopted;
- `.engineering/e2e.json` — E2E environment/evidence routing;
- Git history — implementation history.

During `ITERATION`, documentation may remain pending while behavior is still changing. At `INTEGRATION`, affected durable documentation must describe the exact candidate behavior. Do not churn `current-state.md` for temporary branch syncs.

Completed plans are deleted after durable truth is transferred unless independent audit/regulatory/release value justifies retention.

## Validation health

Run repository-health checks, including:

```bash
python3 scripts/verify_operations.py
python3 scripts/verify_e2e.py
python3 scripts/verify_product_experience.py
```

Report evidence separately as `AGENT_LOCAL`, `REMOTE_AUTOMATED`, or `REAL_ENVIRONMENT`, and report E2E environment/fidelity plus selected UI evidence mode.

Where practical, review gate duration, flake rate, unique regression signal and overlap. Move cheap high-signal gates earlier and expensive low-frequency gates toward integration/release without deleting real safety invariants.

## Agent context discipline

Prefer scoped search and targeted reads over broad ingestion. Do not read generated outputs, dependencies, vendored code or large artifacts unless required. Keep this guide within `.engineering/documentation-policy.json`; procedures belong in Skills and deterministic rules in scripts/CI.

## Stop conditions

Surface conflicts instead of improvising when a request would violate a durable invariant/ADR, leave material ambiguity unresolved, expose secret/private state, create a second source of truth, bypass required migration/security/resource review, delegate automatable validation to the user, publish an integration/release candidate with stale affected docs, claim stronger E2E/environment evidence than executed, or suppress a legitimate gate merely to gain speed.
