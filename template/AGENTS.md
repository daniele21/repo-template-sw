# <PROJECT_NAME> — Coding Agent Guide

Repository-wide routing for coding agents. It owns durable invariants, delivery/validation routing and ownership discovery, not project status or detailed architecture.

## Read only what the task requires

Always read this guide. Then read only:

1. the closest scoped `AGENTS.md`, if present;
2. the canonical architecture/feature/workstream source required by the task;
3. `.engineering/commands.json` for delivery stage, commands, execution capability and integration/release readiness;
4. `.engineering/e2e.json` only for complete-workflow/environment/fidelity/stage/UI-evidence questions;
5. `validate-change` while iterating, `preflight-change` at integration/release readiness, and `remote-preflight` only for required deterministic gates unavailable locally;
6. when `product-ui` applies, the relevant design contracts and `design-product-experience`;
7. owning implementation, direct consumers and nearby tests.

Do not load every plan or all documentation for a local change.

## Repository purpose

<REPLACE_WITH_3_TO_6_LINES_DESCRIBING_THE_PRODUCT_AND_PRIMARY_RUNTIME>

## Non-negotiable invariants

<REPLACE_WITH_PROJECT_SPECIFIC_DURABLE_INVARIANTS>

Keep this list short. Do not repeat generic rules already enforced by Skills/CI unless this project needs a specialization.

## Ownership and routing

| Change | Start here | Inspect next |
| --- | --- | --- |
| <public/domain contract> | <owner path> | <direct consumers/tests> |
| <runtime/lifecycle> | <owner path> | <adapters/persistence/tests> |
| <persistence/data lifecycle> | <owner path> | <migrations/consumers/tests> |
| <UI/transport adapter> | <owner path> | <owning domain contract/tests> |
| <product experience/design system> | `design/ux-contract.json` | `design-product-experience` + <canonical design/component source> |

Add scoped guides only for meaningful local invariants, hazards, ownership or validation commands.

## Delivery stages

`.engineering/commands.json` separates stage from validation depth.

### `ITERATION`

Default while implementation changes. Falsify the current edit quickly with the cheapest useful formatter/static/compile/unit/direct-contract gates. Exact-head publication evidence, complete diff review, durable-doc freshness, remote preflight and release-grade E2E are not default iteration requirements. Draft/collaboration PRs may exist without being integration-ready.

### `INTEGRATION`

Use when a coherent vertical slice provides an observable user/system outcome and is ready to converge into the shared development/integration branch. Refresh base/head, inspect the complete diff, make affected durable docs current, select risk gates, execute/route deterministic evidence and prove affected complete workflows with the smallest sufficient automated critical E2E.

Residual `REAL_ENVIRONMENT` requirements are declared as `DEFERRED_TO_RELEASE`; they do not normally block the branch/PR from entering the shared development branch once required automated evidence is complete.

### `RELEASE`

Use for stable promotion/release/reference checkpoints. Expect `FULL` validation plus release-critical artifact/E2E and every applicable blocking real-environment confirmation before `RELEASE_READY`.

## Project operating commands

`.engineering/commands.json` owns commands/development-velocity/execution routing; `.engineering/e2e.json` owns E2E environment/stage/evidence routing.

- `check` — broad cheap validation;
- `test` — unit/integration/contract behavior;
- `e2e` — complete critical workflow when lower-level tests are insufficient;
- `build` — runnable/build output;
- `smoke` — minimum built/runtime viability;
- `package` — distributable output when relevant;
- `stop`/`clean` — project-owned runtime/generated state.

Do not treat `e2e` and `smoke` as synonyms. Keep E2E small and critical.

## Validation routing

```text
changed outcome
-> risk dimensions
-> required gates
-> LEAN | SCOPED | STRONG | FULL
-> AGENT_LOCAL | REMOTE_AUTOMATED | REAL_ENVIRONMENT
```

Escalate because the changed invariant requires stronger evidence, not because a broad feature label sounds risky. `FULL` is expected for release and exceptional for ordinary feature work.

Route deterministic gates unavailable locally to repository automation; do not make the user the runner. At integration/release, reuse successful evidence matching exact head, material target/base relationship, required gates/profile and relevant E2E environment/evidence mode. PR recreation or draft/ready metadata alone does not invalidate equivalent evidence.

At `INTEGRATION`, every affected automatable gate must pass. Keep real-environment requirements visible but defer them to release. At `RELEASE`, applicable required real-environment gates are blocking.

## E2E routing

Executor and environment fidelity are independent. At integration, use the cheapest automated E2E environment that proves the complete changed outcome and carry only residual target-specific gaps to release.

For UI journeys select evidence from the claim and stage:

- `ASSERTIONS` — UI incidental to deterministic system behavior;
- `SCREENSHOTS` — bounded stable visible layout/hierarchy/copy/state/recovery/adaptive semantics need inspection;
- `FULL_MEDIA` — screenshots plus continuous journey video when UI/UX is materially part of the integration outcome, or when motion, timing/progression, navigation/transition sequence, lifecycle visibility, gesture continuity or release acceptance needs observation over time.

A material UI/UX critical journey entering the shared development branch uses `FULL_MEDIA` by default. UI presence alone does not force video when the UI is merely an incidental harness for a non-visual invariant. Missing evidence required by the selected mode means `E2E_EVIDENCE_INCOMPLETE`.

Final target testing primarily confirms residual fidelity gaps at release.

## Product experience routing

When `product-ui` applies, reason proportionally from user outcome/task -> IA/journey -> hierarchy/disclosure/defaults -> interactions/states/feedback/recovery -> adaptive/accessibility -> design system -> motion/visual polish -> validation.

Structural UX uses the full sequence; interaction changes start from the owning journey/affected layers; visual-only edits preserve settled semantics and use the canonical design-system/brand owner. Do not use polish to compensate for unresolved flow/hierarchy/feedback.

## Core change workflow

1. Confirm owning boundary and smallest coherent outcome; resolve material ambiguity from canonical evidence.
2. Use `plan-workstream` only when persistent dependency/parallel coordination helps.
3. Prefer observable vertical outcomes; technical layers are subtasks unless independently valuable/mergeable/reviewable.
4. Parallel branches should converge early; stacked publication is exceptional.
5. Use `structured-change` for meaningful behavior and `validate-change` during `ITERATION`; diagnose the owning invariant before patching failures.
6. Move to `INTEGRATION` when the slice has an observable outcome.
7. At integration update affected durable docs, refresh base/head, review the complete diff and select required risk gates.
8. Prove affected complete journeys automatically; use `FULL_MEDIA` for material UI/UX journeys and record residual real-environment gaps as deferred.
9. Use `preflight-change`, reuse equivalent successful evidence, then route only missing remote gates through `remote-preflight`.
10. At release close required real-environment gaps before `RELEASE_READY`.
11. Finalize/delete completed workstreams after durable truth transfers.

## Documentation lifecycle

`docs/README.md` owns documentation routing. Key owners: README identity/usage, `docs/architecture.md`, `docs/features/`, `docs/adr/`, `docs/current-state.md`, `docs/workstreams/`, applicable `design/`, `.engineering/e2e.json`, and Git history.

During `ITERATION`, documentation may remain pending while behavior changes. At `INTEGRATION`, affected durable docs describe the exact candidate behavior. `current-state.md` is short integrated/blocked/next truth, not minute-by-minute activity; do not churn it for temporary branch syncs. Completed plans are deleted unless independent audit/regulatory/release value justifies retention.

## Validation health

Run repository health checks including:

```bash
python3 scripts/verify_operations.py
python3 scripts/verify_e2e.py
python3 scripts/verify_stage_environment_policy.py
python3 scripts/verify_product_experience.py
```

Report executor class plus E2E environment/fidelity/evidence mode and stage placement. Where practical, review gate duration, flake rate, unique regression signal and overlap; move cheap high-signal gates earlier, affected automated E2E to integration and expensive real-environment confirmation to release without deleting real safety invariants.

## Agent context discipline

Prefer scoped search/targeted reads. Do not read generated outputs, dependencies, vendored code or large artifacts unless required. Keep this guide within `.engineering/documentation-policy.json`; procedures belong in Skills and deterministic rules in scripts/CI.

## Stop conditions

Surface conflicts instead of improvising when a request would violate a durable invariant/ADR, leave material ambiguity unresolved, expose secrets/private state, create a second source of truth, bypass required migration/security/resource review, delegate automatable validation to the user, publish an integration/release candidate with stale affected docs, overclaim E2E/environment evidence, treat deferred real-environment evidence as already passed, or suppress a legitimate gate merely for speed.
