# Using `repo-template-sw`

This guide explains how to bootstrap, operate and migrate repositories with `repo-template-sw` 0.9.2.

`repo-template-sw` is a **bootstrap, audit and migration source**. After adoption, ordinary work is driven by the target repository itself.

The governing model is **same semantics, native implementation**: preserve Gradle/Xcode/Python/Node/native E2E/design tooling and map it to common engineering behavior rather than replacing it for uniformity.

## Mental model

```text
repo-template-sw
  -> STANDARD.md                      core invariants/maturity
  -> OPERATING-CONTRACT.md            commands/build/artifact/runtime
  -> EXECUTION-CAPABILITY-CONTRACT.md stages + risk gates + executor + evidence reuse
  -> E2E-ENVIRONMENT-CONTRACT.md      environment fidelity + stage policy + UI evidence modes
  -> PRODUCT-EXPERIENCE-CONTRACT.md   optional UX/UI semantics
  -> template/                        adoptable project baseline
  -> profiles/                        stack/domain/product specialization
```

After adoption:

```text
project repository
  -> AGENTS.md                    routing/invariants
  -> .engineering/commands.json  commands + development velocity + validation/preflight
  -> .engineering/e2e.json       E2E environments/stage/journeys/evidence policy
  -> local Skills                recurring workflows
  -> durable docs                current integrated truth
  -> scripts / CI                deterministic enforcement/execution
```

## 1. Ordinary development in 0.9.x

Do not treat every change as a release candidate.

### ITERATION

Default while implementation changes.

Use the cheapest gate that can falsify the current hypothesis:

```text
edit
-> touched format/static check
-> affected compile/typecheck
-> focused unit/component test
-> direct contract/consumer test only when needed
-> repeat
```

Exact-head publication evidence, complete-diff review, durable-doc freshness, remote preflight, broad E2E and release packaging are **not** default requirements here.

A temporary branch or draft PR may exist for collaboration without being integration-ready.

### INTEGRATION

Move here when the work forms a coherent **observable vertical outcome** ready to enter the shared development/integration branch.

Now:

1. refresh exact source head and intended target/base;
2. inspect the complete diff;
3. make affected durable docs current;
4. resolve risk dimensions and concrete required gates;
5. classify each gate as `AGENT_LOCAL`, `REMOTE_AUTOMATED` or `REAL_ENVIRONMENT`;
6. reuse equivalent successful evidence;
7. execute only missing/stale/insufficient deterministic gates;
8. run affected complete critical journeys automatically when lower-level evidence is insufficient;
9. for a material UI/UX critical journey, use `FULL_MEDIA`: bounded screenshot checkpoints plus continuous journey video;
10. record residual `REAL_ENVIRONMENT` gaps as `DEFERRED_TO_RELEASE` rather than blocking ordinary integration.

`AUTOMATED_PREFLIGHT_CONFIRMED` means the exact integration candidate has sufficient automated evidence. It does not claim that deferred physical/target-environment release evidence already passed.

### RELEASE

For stable promotion/release/reference checkpoints:

- use `FULL` validation;
- run release-critical build/package/artifact gates;
- run release-critical automated E2E at sufficient fidelity;
- close every residual real-environment confirmation required by the release claim;
- declare `RELEASE_READY` only when those required residual gates pass.

The practical stage boundary is:

```text
branch / PR -> shared dev
  = automated confidence

dev / integration -> main / stable
  = release confidence + required real environment
```

## 2. Validation depth

Delivery stage and validation depth are separate.

- `LEAN` — docs/governance/metadata or cheap universal guards;
- `SCOPED` — contained owner/module plus direct consumers/tests/lint/compile;
- `STRONG` — cross-boundary/shared contract/persistence/security/lifecycle/native/package/R8/dependency/variant risk;
- `FULL` — release/promotion or unsafe-to-narrow selector/global build/toolchain/dependency-inventory changes.

Prefer a selector that emits:

```text
RISKS: ui_behavior, direct_consumer
REQUIRED_GATES: app compile, focused tests, app lint, consumer contract
PROFILE: SCOPED
```

rather than using file paths to select a giant suite mechanically.

## 3. Remote validation without duplicate CI

When the current agent lacks an SDK/toolchain, deterministic gates are `REMOTE_AUTOMATED`, not user tasks.

Before dispatching remote preflight, search existing successful evidence. For the integration candidate, reuse it when it still matches exact source head, source tree when available, material target/base relationship, required gates, selected profile or stronger equivalent, and relevant E2E environment/fidelity/evidence mode.

This means a recreated PR, draft -> ready transition or metadata-only collaboration change should not rerun unchanged validation solely because PR identity changed.

Normal candidate flow:

```text
required automated gates
-> reuse valid exact-head evidence
-> find remaining automated gaps
-> run only remaining remote gates
-> automated preflight confirmed
-> integrate
```

### 0.9.1 post-merge reuse

A squash/rebase may create a new commit SHA even when the validated source content is unchanged. Repository CI may skip a second heavy integration validation only when all of these are true:

- the post-merge commit Git tree exactly equals the validated candidate tree;
- the push base exactly equals the target/base revision used by candidate validation;
- required gates/profile and relevant E2E identity remain sufficient;
- the evidence comes from trusted repository-owned automation and is current.

Call this `tree-equivalent` reuse, not exact-head reuse. The previous run did not execute on the new commit object; it proved the exact same source tree against the exact same integration base.

A moved base, changed tree, broader gates, expired/missing evidence or direct push without matching proof must validate normally. `RELEASE` remains exact-candidate/reference-grade by default.

## 4. E2E environment fidelity

Do not confuse who runs a test with what environment the test represents.

Typical fidelity ladder:

```text
host_or_fake
-> simulated_or_emulated
-> representative_virtual
-> representative_physical
-> target_environment
```

At integration, use the cheapest declared **automated** environment sufficient to prove the complete changed outcome. Escalate automated fidelity only when the integration claim requires it.

Residual physical/manual/target testing belongs to release acceptance by default and should primarily close fidelity gaps rather than discover ordinary navigation, persistence, IPC or packaging failures that practical automation could catch earlier.

An earlier real-device run remains valid for diagnosis of an explicitly hardware-specific issue. It does not become a standard branch/PR integration gate.

## 5. UI E2E evidence

UI evidence remains risk-based, with one important 0.9.2 stage rule: when UI/UX is materially part of a critical outcome entering the shared development branch, use `FULL_MEDIA` so the integrated experience is directly inspectable.

### ASSERTIONS

Use when UI is incidental to a deterministic system claim, such as persistence, Binder/protocol recovery or process lifecycle.

### SCREENSHOTS

Use for bounded stable-state inspection when the complete UI/UX journey itself is not the material integration claim: hierarchy, layout, copy, recovery state, progressive disclosure or adaptive presentation.

### FULL_MEDIA

Use for a material UI/UX integration journey and whenever sequence over time matters: motion/animation, timing/progression, navigation/transition sequencing, lifecycle visibility, gesture continuity or release/product acceptance.

`FULL_MEDIA` includes required screenshots plus continuous journey video.

Do not downgrade the selected evidence mode after execution to hide missing artifacts. Missing required evidence is `E2E_EVIDENCE_INCOMPLETE`.

## 6. Parallel work and workstreams

Use `plan-workstream` only when persistent dependency/parallel coordination adds value.

Prefer:

```text
vertical outcome
  ├─ subtask A -> temporary branch/worktree
  ├─ subtask B -> temporary branch/worktree
  └─ subtask C -> temporary branch/worktree
          ↓
      early convergence
          ↓
feature/integration branch
          ↓
coherent integration PR
```

A small technical layer is not automatically a vertical slice. Ask what observable behavior becomes true when the slice lands.

Stacked PRs are useful only when each level is independently mergeable/reviewable/value-bearing or separate ownership genuinely requires it. Sync-only PRs indicate avoidable coordination tax.

## 7. Documentation timing

During `ITERATION`, durable docs may remain pending while behavior is unsettled.

At `INTEGRATION`, every affected canonical owner must be current with the exact candidate behavior.

`docs/current-state.md` should describe **integrated / blocked / next** repository truth. Do not update it for every agent commit, branch replay or stack sync.

Completed implementation plans are deleted by default after durable truth is transferred; Git keeps history.

## 8. New repository adoption

1. Read `STANDARD.md` plus only applicable focused contracts/profiles.
2. Copy/specialize `template/`.
3. Replace project placeholders from actual repository evidence.
4. Map canonical command intents to native tooling in `.engineering/commands.json`.
5. Specialize `development_velocity`, risk/gate selector, execution classes and remote-preflight trigger/reuse semantics.
6. Decide E2E applicability and specialize `.engineering/e2e.json` with target environments, execution environments, stage policy, critical journeys, residual gaps and minimum UI evidence mode.
7. If the product has material UI, adopt `product-ui` and point to the real design-system/brand owner.
8. Preserve stronger existing tooling rather than introducing parallel frameworks.
9. Run repository/operations/E2E/stage-policy/product-experience/docs/context verifiers.
10. Record the adopted baseline version only when behavior really matches it.

Useful prompt:

```text
Adopt repo-template-sw 0.9.2 in <REPOSITORY>.
Use adopt-engineering-standard.
Preserve stronger existing engineering/build/E2E/design mechanisms and specialize the template from repository evidence rather than copying placeholders blindly.
```

## 9. Existing repository adoption

Treat adoption as a gap analysis, not wholesale template replacement.

Classify current mechanisms as:

- `KEEP` — already satisfies or exceeds the invariant;
- `ADAPT` — keep mechanism, add missing semantics/routing;
- `ADD` — missing capability genuinely needed;
- `N/A` — concern does not apply.

Preserve project-native commands, CI, test framework, device/provider infrastructure, build/release mechanisms and design sources of truth when they are stronger than the baseline.

## 10. Baseline migration

Use `skills/update-engineering-standard/SKILL.md`.

For each semantic delta classify:

- `APPLY`;
- `MERGE`;
- `N/A`;
- `DEFER`;
- `CONFLICT`.

A version bump without applying or explicitly classifying the semantic delta is not a valid migration.

### Migrating 0.8.1 -> 0.9.0

Key migration work:

1. `.engineering/commands.json` -> operating contract `0.6.0`;
2. add `ITERATION / INTEGRATION / RELEASE` development-velocity routing;
3. update selector output toward risk dimensions + concrete required gates;
4. enable reuse of equivalent successful remote evidence;
5. `.engineering/e2e.json` -> E2E contract `0.2.0` with risk-based UI evidence modes;
6. update validation/preflight/remote-preflight/workstream Skills;
7. move exact-head/full-diff/durable-doc readiness to integration/release rather than every edit/draft update;
8. prefer early convergence of related parallel work over stacked publication;
9. simplify ordinary vertical-slice PR evidence and keep a separate release checklist;
10. specialize stack profiles, especially Android, so expensive gates run where their signal justifies their latency.

Existing screenshot/video capture infrastructure should normally be **kept**. 0.9.0 changes when it is required: use it for `SCREENSHOTS`/`FULL_MEDIA` claims instead of forcing video on every UI-bearing E2E run.

### Migrating 0.9.0 -> 0.9.1

This is an additive validation-economics patch:

1. add `source_tree` to reusable validation evidence identity;
2. declare whether post-merge tree-equivalent reuse is supported;
3. if supported, require exact same candidate tree + exact same target/base + sufficient gates/profile/E2E identity;
4. retain exact-head candidate validation and exact-candidate RELEASE semantics;
5. keep direct pushes and any base/tree mismatch on the normal validation path.

### Migrating 0.9.1 -> 0.9.2

This is a delivery-stage simplification with stronger automated integration semantics:

1. add integration/release stage policy to `.engineering/commands.json` and `.engineering/e2e.json`;
2. require affected complete critical journeys to pass automatically before shared development integration;
3. mark integration `REAL_ENVIRONMENT` evidence non-blocking and explicitly `DEFERRED_TO_RELEASE`;
4. make required release real-environment evidence blocking before `RELEASE_READY`;
5. use `FULL_MEDIA` for material UI/UX critical journeys entering the shared development branch, while keeping assertion-only UI harnesses valid for non-visual system claims;
6. add `verify_stage_environment_policy.py` to repository health;
7. update `preflight-change` so `AUTOMATED_PREFLIGHT_CONFIRMED` and `RELEASE_READY` are distinct readiness claims;
8. for Android, keep emulator/instrumentation/built-APK E2E in integration and move physical/OEM target confirmation to release by default.
