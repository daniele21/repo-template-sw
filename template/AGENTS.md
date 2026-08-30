# <PROJECT_NAME> — Coding Agent Guide

This is the repository-wide routing layer for coding agents. It owns durable invariants, routing and validation selection, not project status or detailed architecture.

## Read only what the task requires

Always read this guide. Then read only:

1. the closest scoped `AGENTS.md` for the target subtree, if present;
2. the canonical architecture/feature/workstream source required by the task; use `docs/README.md` when documentation ownership or impact is unclear;
3. `.engineering/commands.json` for setup/dev/test/E2E/build/runtime/cleanup, execution capability or publication readiness;
4. `.engineering/e2e.json` for complete-workflow, target device/platform/browser/runtime or E2E-fidelity questions;
5. `skills/preflight-change/SKILL.md` before publication and `skills/remote-preflight/SKILL.md` when required deterministic gates cannot run locally;
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

## Project operating commands

`.engineering/commands.json` owns command/publication/execution routing. `.engineering/e2e.json` owns target environments, execution environments, fidelity gaps and critical-journey mapping.

Use the declared intent rather than inventing another path:

- `check` — broad cheap iteration validation;
- `test` — unit/integration/contract behavior;
- `e2e` — complete critical workflow when lower-level tests are insufficient;
- `build` — runnable/build output;
- `smoke` — minimum built/runtime viability;
- `package` — distributable output when relevant;
- `stop`/`clean` — project-owned runtime/generated state.

Do not treat `e2e` and `smoke` as synonyms. Keep E2E small and critical.

For E2E, executor and environment fidelity are independent. `AGENT_LOCAL`, `REMOTE_AUTOMATED` and `REAL_ENVIRONMENT` say who/where executes a gate. `.engineering/e2e.json` says how representative its host/emulator/simulator/virtual/physical/target environment is. A green emulator run is not physical-device evidence.

Use the cheapest automated E2E environment that proves the claim and escalate only when a material target dimension requires it. Final target-environment testing should primarily confirm declared residual gaps, not discover ordinary complete-workflow failures reproducible earlier.

When build/runtime/E2E behavior changes, preserve unique build identity, artifact/build-delta semantics and zero-residue cleanup. Before publishing, `preflight-change` selects required gates, E2E journey/fidelity and executor class. Use `remote-preflight` for deterministic gates unavailable locally; do not turn the user into the test runner because the agent lacks tooling.

## Product experience routing

When `product-ui` is adopted, `design/ux-contract.json` and `design/brand-kit.json` own experience/brand routing. Meaningful UX/UI work follows, at proportional depth:

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
- visual-only — preserve settled flow/semantics and start from the design-system/brand owner.

Do not expose implementation complexity, create duplicate semantic components, or use animation/graphics/polish to compensate for unresolved task flow, hierarchy or feedback.

## Core change workflow

1. Confirm the owning boundary and smallest coherent scope.
2. Resolve material ambiguity from canonical repository evidence; ask the user only when meaningful product/contract alternatives remain.
3. Use `plan-workstream` only when dependency/state coordination is useful.
4. Use `structured-change` before and after meaningful code changes.
5. For meaningful `product-ui` changes, use `design-product-experience` at proportional depth.
6. Inspect owner, direct consumers, fakes and tests before changing shared contracts.
7. Implement one coherent vertical slice without speculative layers.
8. Use `validate-change` for the narrowest sufficient iteration loop; diagnose the owning invariant before patching failures.
9. For affected critical journeys, use `.engineering/e2e.json` to select the cheapest sufficient automated fidelity and explicit residual gaps.
10. Assess documentation impact from the resulting behavior. Update every affected canonical owner in the same change and leave unaffected owners untouched.
11. For README specifically, treat identity and usage separately: update title/summary/`Why this exists` only when core purpose/audience/outcome changed; update setup/run/use/configuration/public examples whenever those instructions changed.
12. Finalize completed workstreams and delete plans by default after durable knowledge transfer.
13. Before publication, use `preflight-change`: refresh target base, inspect the full diff, verify documentation freshness, classify required gates and run all `AGENT_LOCAL` work.
14. Route required `REMOTE_AUTOMATED` gates through `remote-preflight`; inspect, fix and retrigger until complete or genuinely blocked.

## Validation routing

Run repository-health checks, including:

```bash
python3 scripts/verify_operations.py
python3 scripts/verify_e2e.py
python3 scripts/verify_product_experience.py
```

`verify_e2e.py` verifies E2E applicability/environment/fidelity/journey routing. `verify_product_experience.py` is `N/A` unless `product-ui` is adopted. Project commands remain in `.engineering/commands.json`.

Report evidence separately:

- `AGENT_LOCAL` — current agent executed it;
- `REMOTE_AUTOMATED` — repository-owned automation executed it;
- `REAL_ENVIRONMENT` — physical/device/external/manual evidence automation cannot truthfully replace.

For E2E also report the environment/fidelity from `.engineering/e2e.json` and residual gaps. Missing required real-device/hardware/usability evidence stays pending; synthetic/emulator evidence cannot satisfy a stronger claim. Traces/screenshots/videos/logs are bounded evidence artifacts, not durable docs.

## Documentation lifecycle

`docs/README.md` owns documentation routing and the documentation-impact contract.

- README identity — title/summary/`Why this exists`; stable unless core purpose, primary audience or primary outcome changes.
- README usage — setup/run/use/configuration/public examples; must remain executable and truthful for the current repository.
- `docs/architecture.md` — current architecture/ownership.
- `docs/features/` — durable feature behavior when needed; existing feature owners change with the behavior they describe.
- `docs/adr/` — accepted durable decisions.
- `docs/current-state.md` — single short repository-level operational ledger.
- `docs/workstreams/` — active bounded implementation plans only.
- `design/` — product experience/brand contracts and bounded key references when `product-ui` is adopted.
- `.engineering/e2e.json` — current E2E environment/fidelity routing.
- Git history — implementation history.

Code and durable documentation ship together. Do not rewrite stable README identity copy merely because usage changed, and do not leave stale setup/run/use instructions merely because mission copy remains valid.

Completed plans are deleted after durable truth is transferred unless independent audit/regulatory/release/historical value justifies retention. Generated build deltas and E2E/visual evidence are artifacts, not project-status docs.

## Agent context discipline

Prefer scoped search and targeted reads over broad ingestion. Do not read generated outputs, dependencies, vendored code or large artifacts unless required. Keep this guide within `.engineering/documentation-policy.json`; conditional procedures belong in Skills and deterministic rules in scripts/CI.

## Stop conditions

Surface the conflict instead of improvising when a request would violate a durable invariant/ADR, leave material product/contract ambiguity unresolved, expose secret/private state, create a second source of truth, bypass required destructive/migration review, bypass canonical command/test/E2E/environment-fidelity/build/artifact/publication rules, delegate automatable deterministic validation to the user because the agent lacks execution capability, bypass an adopted product-experience contract, publish behavior with stale affected canonical documentation, or claim evidence that was not executed.
