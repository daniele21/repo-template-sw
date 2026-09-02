---
name: validate-change
description: Select the cheapest sufficient validation while iterating, expand evidence only when risk or delivery stage requires it, and diagnose failures at their owning invariant without confusing execution capability with environment fidelity.
---

# Validate Change

## Principle

Optimize for **sufficient confidence per unit of feedback time**.

Do not run the entire repository for every edit, and do not stop at a local unit test when a shared contract, runtime boundary or critical user outcome changed.

Read `.engineering/commands.json` for delivery stage, validation routing and execution capability. Read `.engineering/e2e.json` only when a complete workflow/environment-dependent claim is affected. When `product-ui` is adopted and user-facing semantics change, also read the product-experience contract.

`validate-change` owns the edit/test loop. `preflight-change` starts when a slice is being declared `INTEGRATION`-ready or `RELEASE`-ready.

## 1. Establish delivery stage

### `ITERATION`

Default while implementation is still changing.

Prefer a feedback loop around the affected owner:

- formatter/static checks for touched surface;
- focused unit/component tests;
- affected module/package compile/typecheck;
- direct contract/consumer test only when the edit crosses that boundary.

Do **not** require exact-head publication evidence, complete diff review, durable-documentation freshness, remote preflight, minified/release packaging, broad AndroidTest assembly, emulator E2E or UI media merely because they exist in the repository.

If the current agent cannot run one of these cheap deterministic gates locally, record it for later remote execution unless that gate is necessary to falsify the current hypothesis now.

### `INTEGRATION`

Use when a coherent vertical slice has an observable outcome and is ready to converge.

Expand to the required risk cone:

- affected owners/modules and direct consumers;
- relevant lint/static analysis;
- integration/contract tests implicated by the change;
- build/package/minification only when their semantics are affected;
- the smallest affected critical E2E journey when lower-level evidence cannot prove the slice outcome.

Hand final exact-head readiness to `preflight-change`.

### `RELEASE`

Use for release/promotion/reference checkpoints. Expect `FULL` plus release-critical artifact/E2E gates and residual environment evidence.

## 2. Select risks and gates before profile

Determine the changed risk dimensions, then map them to required gates. Use `LEAN | SCOPED | STRONG | FULL` as a summary.

Examples:

- UI copy/layout with unchanged domain semantics -> affected UI compile/tests/lint, maybe screenshots; normally `SCOPED`;
- Binder/shared protocol -> owner + direct consumer compatibility + relevant integration; normally `STRONG`;
- persistence migration -> migration/recovery/direct consumers; normally `STRONG`;
- native/JNI/package/R8/manifest/variant -> affected native/package/release gates; normally `STRONG`;
- selector/global Gradle/toolchain/dependency-inventory change -> `FULL` because narrowing machinery changed;
- docs/governance only -> `LEAN`.

Do not escalate merely because a broad product area such as “Local AI” is mentioned. Escalate because the changed invariant requires stronger evidence.

## 3. Validation ladder

Use only the rungs needed by the claim.

### A — owner-local

Formatter/static analysis, focused unit/component tests, module compile/typecheck.

### B — direct consumers

Contract/fake compatibility, persistence/migration tests, directly affected adapters/consumers and component-state tests.

### C — integration/build

Relevant repository check/test subsets, integration tests and build/package/minification gates when the changed risk requires them.

### D — complete journey

Use the smallest critical E2E journey when the claim crosses the assembled user/system workflow and lower-level evidence is insufficient.

### E — residual real environment

Use only when the claim genuinely depends on physical hardware, target runtime/OEM behavior, protected authority, representative usability or another dimension automation cannot truthfully reproduce.

Do not execute every rung mechanically.

## 4. E2E environment and UI evidence

When E2E is relevant:

1. identify the affected critical journey;
2. select the cheapest declared execution environment with sufficient fidelity;
3. select UI evidence mode from the changed claim;
4. escalate environment fidelity or evidence mode only when a material dimension requires it.

UI evidence modes:

- `ASSERTIONS` — UI is incidental; changed truth is deterministic system behavior;
- `SCREENSHOTS` — visible hierarchy/layout/copy/state/adaptive/recovery semantics changed;
- `FULL_MEDIA` — motion, timing/progression, navigation/transition sequence, lifecycle visibility, gesture continuity or release/product acceptance depends on observing the journey over time.

A UI process existing does not by itself force `FULL_MEDIA`.

Evidence required by the selected mode must be present and identity-bearing. Missing required artifacts means `E2E_EVIDENCE_INCOMPLETE`; never downgrade the mode after execution merely to claim PASS.

## 5. Product experience validation

When `product-ui` is adopted, validate only the experience layers materially changed:

- structural UX -> outcome/task/IA/hierarchy/progressive disclosure/states/accessibility/adaptive behavior before polish;
- interaction -> owning journey, feedback/recovery, accessibility/adaptive behavior and relevant motion;
- visual-only -> settled semantics preserved, canonical tokens/components reused.

Do not force broad E2E or full-media evidence for a token-only change unless the affected visual risk genuinely needs it.

## 6. Failure diagnosis

Classify every red gate before changing production code:

- current-change regression;
- baseline/pre-existing failure;
- environment/toolchain/dependency issue;
- flaky/non-deterministic behavior;
- stale-base/stack integration effect;
- incorrect requirement/design/contract assumption.

Identify the violated invariant and owner. Fix the owner and add regression evidence at the lowest useful level.

Never suppress/delete/weaken a legitimate failing test merely to make the branch green. If the same gate fails after a repair, form a new falsifiable hypothesis before another patch.

## 7. Operational validation

When relevant, preserve:

- unique build/source identity;
- immutable successful artifact promotion;
- build delta/retention semantics;
- bounded temporary/test/media artifacts;
- zero project-owned process/listener/resource residue after success and failure;
- truthful environment/fidelity reporting.

## 8. Workflow

1. Identify owner, changed observable behavior and delivery stage.
2. Identify risk dimensions.
3. Run the cheapest deterministic gate that can falsify the current edit.
4. Diagnose failures before editing again.
5. Add direct-consumer/integration gates only as boundaries are crossed.
6. During `ITERATION`, keep the loop narrow and avoid publication ceremony.
7. When the vertical slice produces an observable outcome, move to `INTEGRATION`.
8. Select E2E journey/environment/evidence mode only when required by the claim.
9. Record exact executed evidence and remaining deterministic/real-environment gaps.
10. Hand integration/release readiness to `preflight-change`.

## Output

Report:

```text
STAGE: ITERATION|INTEGRATION|RELEASE
RISKS: <dimensions>
PROFILE: LEAN|SCOPED|STRONG|FULL
GATES:
  <gate>: PASS|FAIL|PENDING|N/A / AGENT_LOCAL|REMOTE_AUTOMATED|REAL_ENVIRONMENT
E2E:
  <journey>: <environment>/<fidelity>/<ASSERTIONS|SCREENSHOTS|FULL_MEDIA> / PASS|FAIL|PENDING|N/A
NEXT: <smallest useful next validation/integration action>
```

Absence of agent-local tooling is not evidence that the user must run the gate.
