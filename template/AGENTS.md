# <PROJECT_NAME> — Coding Agent Guide

This file is the repository-wide navigation layer for coding agents. It owns durable invariants, routing and validation selection. It is not a project-status ledger or a substitute for architecture/feature documentation.

## Read only what the task requires

Always read this guide. Then read only:

1. the closest scoped `AGENTS.md`, when one exists for the target subtree;
2. the canonical architecture/feature/workstream source required by the task;
3. `.engineering/commands.json` when setup/dev/test/E2E/build/package/runtime/cleanup behavior is relevant;
4. when `product-ui` is adopted and user-facing behavior/visual semantics change, `design/ux-contract.json`, `design/brand-kit.json` and `skills/design-product-experience/SKILL.md` for meaningful UX/UI work;
5. the owning implementation, direct consumers and nearby tests.

Do not load every plan or all documentation for a local change.

## Repository purpose

<REPLACE_WITH_3_TO_6_LINES_DESCRIBING_THE_PRODUCT_AND_PRIMARY_RUNTIME>

## Non-negotiable invariants

<REPLACE_WITH_PROJECT_SPECIFIC_DURABLE_INVARIANTS>

Keep this list short. Do not copy generic advice already enforced by the standard, Skills or CI unless the project needs a local specialization.

## Ownership and routing

| Change | Start here | Inspect next |
| --- | --- | --- |
| <public/domain contract> | <owner path> | <direct consumers/tests> |
| <runtime/lifecycle> | <owner path> | <adapters/persistence/tests> |
| <persistence/data lifecycle> | <owner path> | <migrations/consumers/tests> |
| <UI/transport adapter> | <owner path> | <owning domain contract/tests> |
| <product experience / design system, if applicable> | `design/ux-contract.json` | `skills/design-product-experience/SKILL.md` + <canonical design/component source + critical journeys> |

Add scoped `AGENTS.md` files only for subtrees with meaningful local invariants, hazards, ownership or validation commands.

## Project operating commands

Canonical repository-level command routing lives in `.engineering/commands.json`.

Use the declared intent rather than inventing a second command path:

- `check` for broad cheap validation while iterating;
- `test` for unit/integration/contract behavioral validation;
- `e2e` when the claim crosses a complete critical user/system workflow boundary and lower-level tests are insufficient;
- `build` when runnable/build output is affected;
- `smoke` when minimal runtime/built-artifact viability must be proven;
- `package` only when distributable output is relevant;
- `stop`/`clean` for project-owned runtime/generated state.

Do not treat `e2e` and `smoke` as synonyms. Keep E2E small and focused on critical journeys; prefer lower-level tests for deterministic invariants.

The underlying command remains native to this repository. When build/runtime/E2E behavior is affected, preserve unique build identity, artifact/build-delta semantics and zero-residue cleanup required by the local operating contract.

## Product experience routing

When `.engineering/baseline.json` includes `product-ui`, `design/ux-contract.json` and `design/brand-kit.json` are canonical routing surfaces for user-facing experience and brand/design-system constraints.

For meaningful UX/UI work, use `design-product-experience` and preserve this decision order at the depth justified by the change:

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

Classify the change first:

- structural UX — use the full sequence;
- interaction — start from the owning task/journey and cover changed interaction/state/accessibility/motion layers;
- visual-only — preserve the settled flow, start from the design-system/brand owner and keep the change local.

Do not make a screen denser or expose internal architecture merely because the implementation exposes more options. Do not create a new visual component when the canonical design system already owns the semantic role. Do not use animation, graphics or polish to compensate for an unresolved task flow, hierarchy or feedback model.

## Core change workflow

1. Confirm the owning boundary and smallest coherent scope.
2. Use `plan-workstream` only for work large enough to need dependency/state coordination.
3. Use `structured-change` before and after meaningful code changes.
4. If `product-ui` is adopted and the change meaningfully affects UX/UI semantics, use `design-product-experience` before implementation; do not invoke a full UX exercise for a genuinely visual-only local edit.
5. Inspect owner, direct consumers, fakes and tests before changing a shared contract.
6. Implement one coherent vertical slice without speculative layers.
7. Use `validate-change` to choose the narrowest sufficient validation while iterating, then expand according to blast radius.
8. Update only the canonical durable document/design contract whose current behavior/decision changed.
9. When an active workstream completes, use `finalize-workstream` to transfer durable knowledge and delete the plan by default.
10. Inspect the complete diff before publishing.

## Validation routing

Run the repository-health checks, including:

```bash
python3 scripts/verify_operations.py
python3 scripts/verify_product_experience.py
```

`verify_product_experience.py` is `N/A` unless `product-ui` is adopted. Use `.engineering/commands.json` for project-specific targeted/full command routing instead of duplicating command strings here.

A missing real-device/hardware/usability run must be reported as pending; never promote synthetic evidence into a stronger claim. E2E/visual traces/screenshots/videos/logs are bounded evidence artifacts, not durable repository docs.

## Documentation lifecycle

- `docs/architecture.md` owns current architecture/ownership.
- `docs/features/` owns durable feature behavior when additional documentation is needed.
- `docs/adr/` owns accepted durable architectural decisions.
- `docs/current-state.md` is the single short repository-level operational ledger.
- `docs/workstreams/` contains only active bounded implementation plans.
- `design/` owns project experience/brand contracts and bounded key reference views when `product-ui` is adopted.
- Completed plans are deleted after durable behavior/decisions are transferred. Archive only with independent audit/regulatory/release/historical justification.
- Git history owns implementation history.

Do not create plan/progress/status documents that duplicate the same workstream. Generated per-build `BUILD_CHANGELOG.md` files and per-run E2E/visual evidence are artifact evidence, not project-status docs.

## Agent context discipline

Prefer scoped search (`rg`, targeted file reads, symbol/caller discovery) over broad file ingestion. Do not read generated outputs, dependency trees, vendored code or large artifacts unless the task requires them.

Keep this guide within the configured budget in `.engineering/documentation-policy.json`. Put conditional procedures in Skills and deterministic rules in scripts/CI rather than growing this file.

## Stop conditions

Surface the conflict instead of improvising when a requested change would violate a durable invariant/accepted ADR, expose secret/private state, create a second source of truth, bypass required review for destructive/migrating behavior, bypass canonical command/test/E2E/build/artifact lifecycle, bypass an adopted product-experience/design-system contract, or claim evidence that was not executed.
