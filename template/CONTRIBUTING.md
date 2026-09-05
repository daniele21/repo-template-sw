# Contributing

## Change scope

Prefer the smallest coherent **vertical outcome** that preserves repository invariants. Inspect the owning boundary, direct consumers and tests before changing shared contracts.

Use a workstream plan only when dependency/state coordination adds real value. Small changes should not create planning documents.

Parallel branches may own separate subtasks, but related work should converge early onto the coherent feature/integration outcome. Stacked publication is exceptional; a sync-only PR is a coordination smell.

Resolve material ambiguity from canonical repository evidence before implementation. If two reasonable interpretations remain and would materially alter behavior, contracts, persistence/migration, security/trust, failure/resource/lifecycle semantics, compatibility, acceptance criteria or meaningful UX, ask the owner instead of silently selecting one.

## Delivery stages

`.engineering/commands.json` declares `ITERATION`, `INTEGRATION` and `RELEASE` semantics separately from `LEAN`, `SCOPED`, `STRONG` and `FULL` validation depth.

### ITERATION

Use the cheapest checks that can falsify the current edit. Exact-head readiness, complete diff review, durable-doc freshness, remote preflight and release-grade E2E are not default requirements for every private edit or draft collaboration update.

### INTEGRATION

When a coherent slice provides an observable outcome, refresh head/base, inspect the complete diff, make affected durable docs current, select risk gates and prove affected complete workflows with automated evidence before the slice enters the shared development/integration branch.

Residual `REAL_ENVIRONMENT` requirements are declared but do not normally block integration; carry them as `DEFERRED_TO_RELEASE`.

### RELEASE

Use full release/reference-grade validation, release-critical E2E/artifact evidence and every applicable blocking real-environment confirmation before `RELEASE_READY`.

## Canonical project commands

`.engineering/commands.json` is the canonical repository-level mapping for `setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop` and `clean`, plus development-velocity, publication and execution routing.

`.engineering/e2e.json` is the canonical E2E environment/fidelity/stage/evidence mapping.

Use project-native tooling behind those intents. Do not introduce a second undocumented build/test/E2E/run path merely for convenience.

## Product experience changes

When `product-ui` is adopted, `design/ux-contract.json` and `design/brand-kit.json` are canonical experience/brand routing surfaces.

Before meaningful UI work, inspect the user task, hierarchy/progressive disclosure, critical states, accessibility/adaptive behavior, design-system owner and critical-journey evidence.

Prefer existing semantic components/tokens over visually duplicative one-offs.

## Validation

Select validation in this order:

```text
changed outcome
-> risk dimensions
-> concrete required gates
-> LEAN | SCOPED | STRONG | FULL summary
-> AGENT_LOCAL | REMOTE_AUTOMATED | REAL_ENVIRONMENT executor
```

Run the narrowest useful checks while iterating, then all required automated integration/repository gates for the actual risk cone. Do not suppress failing tests or weaken a gate merely to make a change green.

When a gate fails, classify it before changing production code: current-change regression, baseline failure, environment/toolchain issue, flaky behavior, stale-base effect or incorrect assumption/contract. Fix the owning invariant rather than applying unexplained symptom patches.

Run repository health checks before publishing engineering-governance changes:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_operations.py
python3 scripts/verify_e2e.py
python3 scripts/verify_stage_environment_policy.py
python3 scripts/verify_product_experience.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

For required gates:

- run `AGENT_LOCAL` work directly;
- use repository automation for deterministic `REMOTE_AUTOMATED` work;
- reserve `REAL_ENVIRONMENT` for genuinely representative hardware/external authority/manual judgement;
- at `INTEGRATION`, report required real-environment evidence as deferred rather than making it the recurring PR test loop;
- at `RELEASE`, require applicable blocking real-environment evidence to pass.

Do not ask the user to run an automatable deterministic command merely because the current agent lacks tooling.

## Remote evidence reuse

Before triggering expensive remote preflight, reuse successful evidence that remains sufficient for the exact source head, material target/base relationship, required gates/profile and E2E environment/evidence mode.

A replacement PR, draft/ready transition, label/comment or other collaboration metadata change does not invalidate equivalent source evidence by itself.

Rerun only missing, stale or insufficient gates.

## E2E

Use E2E only when a complete critical outcome needs to be proven across assembled boundaries and lower-level tests are insufficient. `smoke` is not a substitute for E2E.

At integration, select the affected critical journey plus the cheapest automated environment whose fidelity is sufficient for the complete changed outcome. Carry only residual physical/target-specific gaps to release.

For UI journeys select evidence mode based on the changed claim and stage:

- `ASSERTIONS` — UI is incidental to deterministic system behavior;
- `SCREENSHOTS` — bounded stable visible layout/hierarchy/copy/state/recovery/adaptive semantics need inspection;
- `FULL_MEDIA` — screenshots plus continuous video when UI/UX is materially part of the integration outcome, or when motion, timing/progression, navigation/transition sequence, lifecycle visibility, gesture continuity or release/product acceptance depends on observing the journey over time.

A material UI/UX critical journey entering the shared development branch uses `FULL_MEDIA` by default. UI presence alone does not force video when the UI is merely an incidental harness for a non-visual invariant. Missing evidence required by the **selected mode** is `E2E_EVIDENCE_INCOMPLETE`.

Keep E2E evidence identity-bearing, privacy-safe and bounded. Clean project-owned servers/listeners, browser/device sessions, test data and temporary state after success/failure/cancellation.

## Build/runtime/package behavior

Validate applicable operating invariants: unique build identity, immutable/promoted artifacts, manifest/checksum/build delta, bounded retention, graceful stop and zero project-owned resource residue.

## Integration/release readiness

Use `skills/preflight-change/SKILL.md` when the change moves from iteration to integration/release readiness — not before every push or draft update.

Preflight must:

- establish exact head/base;
- review the complete diff;
- verify affected durable documentation is current;
- select risks/gates/profile;
- select affected automated E2E and UI evidence mode;
- reuse equivalent successful evidence;
- execute/route only unsatisfied deterministic/automated gates;
- classify residual real-environment gaps separately.

`AUTOMATED_PREFLIGHT_CONFIRMED` means every automated gate required by the exact integration candidate is satisfied by valid current evidence. Real-environment evidence may remain explicitly `DEFERRED_TO_RELEASE`.

`RELEASE_READY` additionally requires every applicable blocking real-environment confirmation to pass.

Final target-environment testing should primarily close declared residual fidelity gaps. If it repeatedly discovers ordinary workflow failures reproducible earlier, move that evidence into automated integration E2E.

## Dependencies and architecture

Avoid dynamic versions and speculative dependencies. New abstractions/dependencies need a concrete owner/problem and must not duplicate a source of truth.

## Pull requests

Keep integration PRs focused on an observable outcome. Describe scope/risks, required gates, reused/new evidence, affected automated E2E, UI screenshots/video when required, affected durable documentation and remaining residual release gaps.

Use the release PR template for `RELEASE` checkpoints instead of turning every ordinary PR into a release dossier.

A known-red draft may be used for explicit collaboration/investigation but must not be represented as integration-ready.

Canonical branches should be protected with pull requests and required checks according to the project's branching/release model.
