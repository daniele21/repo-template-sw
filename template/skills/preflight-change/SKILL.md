---
name: preflight-change
description: Establish exact-head readiness for an integration or release candidate by resolving material ambiguity, refreshing base/diff/docs, selecting risk gates and E2E fidelity, reusing equivalent successful evidence, and routing only missing deterministic work.
---

# Preflight Change

Use this Skill when a coherent vertical slice is being declared **INTEGRATION-ready** or **RELEASE-ready**: for example before marking a PR ready, merging into the shared integration branch, promoting toward stable/release, or publishing a release candidate.

Do **not** invoke full publication ceremony for every implementation edit, temporary branch push or draft/collaboration PR update. Those remain `ITERATION` and belong to `validate-change`.

The governing rules are:

> Delivery stage and validation depth are independent.

> Exact-head, complete-diff and durable-documentation readiness start at `INTEGRATION`.

> `INTEGRATION` proves the coherent outcome with automated evidence. Required `REAL_ENVIRONMENT` confirmation is carried explicitly to `RELEASE`, not used as a normal blocker for merging into the shared integration branch.

> Select required risk gates first; validation profiles summarize the result.

> Reuse successful equivalent evidence before starting a new expensive run.

> Automatable deterministic gates are never delegated to the user merely because the current agent lacks tooling.

## 1. Confirm stage and observable outcome

Record `INTEGRATION` or `RELEASE` and state the user/system outcome the candidate now delivers.

A technical layer that does not independently provide an observable slice outcome should normally remain part of a larger integration slice unless independent publication/review is genuinely useful.

`RELEASE` expects `FULL`. `INTEGRATION` uses the narrowest sufficient risk profile and must satisfy the affected automated critical journeys.

## 2. Resolve material ambiguity

Inspect canonical owners, durable docs/ADRs, direct consumers/fakes and active workstream acceptance criteria.

Ask the user only if reasonable alternatives remain that materially change product behavior, public/API/protocol contracts, persistence/migration, security/trust/privacy, lifecycle/resource semantics, compatibility, acceptance criteria or meaningful UX.

Do not ask about local implementation choices that preserve observable semantics.

## 3. Refresh intended base and exact head

Record exact target/base revision and candidate head revision.

Verify the candidate is based on, reconciled with or proven merge-compatible with the intended target according to repository policy.

Invalidate only evidence affected by a material head/base/dependency relationship change. PR recreation, draft/ready metadata or other collaboration-only changes do not invalidate equivalent source evidence by themselves.

## 4. Review the complete diff

Inspect the whole diff against the intended base for:

- unrelated/generated/private/debug residue;
- hidden scope expansion;
- duplicate ownership/policy;
- weakened/suppressed tests;
- stale affected docs/contracts;
- missed direct consumers/fakes/adapters;
- compatibility/migration/security/resource/UX drift;
- changed E2E environment assumptions.

This complete-diff review is required for integration/release readiness, not for every private edit.

## 5. Make durable documentation current

Assess resulting observable behavior and update only affected canonical owners.

At minimum classify:

- `README_IDENTITY`;
- `README_USAGE`;
- `FEATURE_DOCS`;
- `ARCHITECTURE`;
- `ADR`;
- `SECURITY_DATA`;
- `OPERATIONS`;
- `PRODUCT_EXPERIENCE`;
- `CURRENT_STATE`.

`docs/current-state.md` describes integrated/blocked/next repository truth, not minute-by-minute agent activity. Temporary implementation branches do not need to churn it.

During `ITERATION`, durable docs may remain pending. At `INTEGRATION`, every affected durable owner must be current with the candidate.

## 6. Select risk dimensions, gates and profile

Read `.engineering/commands.json` and run the project selector.

Prefer output shaped as:

```text
RISKS: <risk dimensions>
REQUIRED_GATES: <concrete gates>
PROFILE: LEAN|SCOPED|STRONG|FULL
```

Use:

- `LEAN` — docs/governance/metadata or cheap universal guards;
- `SCOPED` — contained owner/module plus direct consumers/tests/lint/compile;
- `STRONG` — public/shared contracts, persistence/security, native/JNI, packaging/R8/manifest/dependency/variant, lifecycle/resource or multi-owner integration;
- `FULL` — release/promotion, selector/global build/toolchain/dependency-inventory changes, unknown executable scope or explicit full.

Do not escalate based on broad feature labels. Escalate because a risk dimension requires stronger evidence.

## 7. Select E2E journey, environment and UI evidence mode

When lower-level evidence cannot prove the complete affected outcome, read `.engineering/e2e.json` and select:

1. smallest affected critical journey;
2. cheapest sufficient **automated** environment/fidelity;
3. UI evidence mode required by the claim and delivery stage.

At `INTEGRATION`:

- affected critical journeys must be exercised automatically before merging into the shared integration branch;
- if UI/UX is materially part of the observable outcome, use `FULL_MEDIA`: bounded screenshot checkpoints plus one continuous journey video;
- if UI is only an incidental harness for a non-visual invariant, `ASSERTIONS` may remain sufficient;
- stable visual-only evidence may still use `SCREENSHOTS` when the configured journey is not a material UI/UX integration outcome.

At `RELEASE`, use release-critical journeys and any stronger media/evidence required for final product acceptance.

Do not promote emulator/simulator evidence into a physical-device claim. Automated integration evidence proves the complete workflow at its declared fidelity; residual physical/target gaps are carried to release.

## 8. Build the gate matrix

For every required gate assign:

- `AGENT_LOCAL`;
- `REMOTE_AUTOMATED`;
- `REAL_ENVIRONMENT`.

Execution capability and E2E environment fidelity remain separate.

Ordinary formatter/compile/lint/unit/R8/package/emulator work is not `REAL_ENVIRONMENT` merely because the current agent lacks the SDK.

At `INTEGRATION`, classify and report residual `REAL_ENVIRONMENT` needs but do **not** execute or block the integration candidate on them. They become release acceptance requirements.

At `RELEASE`, every `REAL_ENVIRONMENT` gate marked required by the product claim must pass before the strongest stable/release claim is made.

## 9. Reuse equivalent successful evidence first

Before triggering remote work, inspect existing successful validation evidence.

Reuse evidence only when it remains sufficient for:

- exact candidate head;
- material target/base relationship;
- required gates;
- selected profile or a stronger equivalent profile;
- selected E2E environment/fidelity/evidence mode where relevant.

Do not rerun merely because:

- a draft PR was recreated as ready;
- PR number changed but source head/base/gates did not;
- collaboration metadata changed;
- another successful workflow already proved the exact same gate set.

Rerun only missing, stale or insufficient evidence.

## 10. Execute/reroute remaining deterministic gates

Run required `AGENT_LOCAL` gates.

For required `REMOTE_AUTOMATED` gates not already satisfied by reusable evidence, hand off to `remote-preflight` immediately.

Do not ask the user to execute automatable deterministic work.

For E2E, verify evidence required by the selected UI mode exists. Missing required artifacts means `E2E_EVIDENCE_INCOMPLETE`; never downgrade the selected mode after the fact to claim PASS.

At `INTEGRATION`, unresolved real-environment evidence is reported as `DEFERRED_TO_RELEASE` and does not prevent `AUTOMATED_PREFLIGHT_CONFIRMED` when all required automated evidence is complete.

At `RELEASE`, required real-environment evidence is a blocking acceptance gate.

## 11. Failure loop

Classify failures as:

- `CHANGE_REGRESSION`;
- `BASELINE_FAILURE`;
- `ENVIRONMENT`;
- `FLAKY`;
- `BASE_DRIFT`;
- `ASSUMPTION`.

Fix the owning cause, re-evaluate risk/gates, and invalidate/rerun only affected evidence. A repair that broadens risk may legitimately escalate the profile.

Never suppress a legitimate gate or rerun an unrelated full suite as a substitute for diagnosis.

## 12. Output readiness

Report:

```text
STAGE: INTEGRATION|RELEASE
HEAD: <revision>
TARGET: <branch>@<revision>
OUTCOME: <observable slice/release outcome>
AMBIGUITY: PASS|FAIL
BASE_FRESHNESS: PASS|FAIL
FULL_DIFF_REVIEW: PASS|FAIL
DOCUMENTATION_IMPACT:
  README_IDENTITY: UPDATED|N/A
  README_USAGE: UPDATED|N/A
  FEATURE_DOCS: UPDATED|N/A
  ARCHITECTURE: UPDATED|N/A
  ADR: UPDATED|N/A
  SECURITY_DATA: UPDATED|N/A
  OPERATIONS: UPDATED|N/A
  PRODUCT_EXPERIENCE: UPDATED|N/A
  CURRENT_STATE: UPDATED|N/A
DOCS_CURRENT_WITH_IMPLEMENTATION: PASS|FAIL
RISKS: <dimensions>
VALIDATION_PROFILE: LEAN|SCOPED|STRONG|FULL
REQUIRED_GATES: <list>
REUSED_EVIDENCE: <gate/run refs or N/A>
AGENT_LOCAL:
  <gate>: PASS|FAIL|N/A
REMOTE_AUTOMATED:
  <gate>: PASS|FAIL|PENDING|N/A
E2E:
  <journey>: <environment>/<fidelity>/<ASSERTIONS|SCREENSHOTS|FULL_MEDIA> / PASS|FAIL|PENDING|N/A
REAL_ENVIRONMENT:
  <gate>: DEFERRED_TO_RELEASE|PASS|FAIL|PENDING|N/A
READINESS: READY_FOR_CI|READY_FOR_REMOTE_PREFLIGHT|AUTOMATED_PREFLIGHT_CONFIRMED|RELEASE_READY|NOT_READY_FOR_AUTOMATED_PREFLIGHT
```

At `INTEGRATION`, `AUTOMATED_PREFLIGHT_CONFIRMED` means every deterministic automated gate and affected automated E2E requirement for the exact candidate is satisfied; residual real-environment evidence may remain `DEFERRED_TO_RELEASE`.

At `RELEASE`, `RELEASE_READY` additionally requires every applicable blocking `REAL_ENVIRONMENT` gate to pass.
