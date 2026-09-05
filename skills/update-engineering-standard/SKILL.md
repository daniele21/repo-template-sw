---
name: update-engineering-standard
description: Migrate an already-adopted repository from its recorded repo-template-sw baseline to a newer version through an explicit semantic delta review that preserves local engineering/product customizations and applies only relevant changes.
---

# Update Engineering Standard

## Principle

An adopted repository is self-contained. Standard updates are explicit semantic migrations, not automatic synchronization or template overwrites.

A version bump without applying or explicitly classifying relevant behavioral deltas is not a valid migration.

## Workflow

1. Read the target repository's `.engineering/baseline.json`, `.engineering/commands.json`, `.engineering/e2e.json`, relevant local Skills/guides and design contracts when `product-ui` applies.
2. Read `VERSION`, `CHANGELOG.md`, `STANDARD.md` and changed focused contracts in `repo-template-sw` from the recorded version to the requested target.
3. Classify each delta:
   - `APPLY` — relevant and local mechanism is effectively baseline-compatible;
   - `MERGE` — relevant but local mechanism is customized/stronger;
   - `N/A` — profile/concern does not apply;
   - `DEFER` — intentionally postponed with reason/owner;
   - `CONFLICT` — requires explicit architecture/product decision.
4. Inspect actual local behavior before replacing text or config.
5. Preserve stronger project-native commands, CI scope detectors, E2E frameworks/device providers, build/release systems, design systems and security boundaries.
6. Implement the smallest migration that makes the new semantics real.
7. Validate the migration itself at the appropriate risk/stage.
8. Update contract versions/mappings only after behavior matches them.
9. Update `.engineering/baseline.json` and per-Skill `source_version` last, preserving `customized: true` where intentional local divergence remains.
10. Update durable project docs only when current behavior/ownership changed.
11. Delete migration workstreams after durable truth is transferred unless independent archive/audit value exists.

## Historical migration principle

Older baseline deltas remain documented in `CHANGELOG.md` and their focused contracts. When migrating across several versions, apply the **semantic end state** in dependency order rather than mechanically replaying every obsolete intermediate implementation.

Examples:

- operating/build/artifact semantics are owned by `OPERATING-CONTRACT.md`;
- execution/no-human-runner/validation routing by `EXECUTION-CAPABILITY-CONTRACT.md`;
- E2E fidelity/evidence by `E2E-ENVIRONMENT-CONTRACT.md`;
- product experience by `PRODUCT-EXPERIENCE-CONTRACT.md`.

Preserve stronger existing mechanisms and avoid adding parallel wrappers/frameworks solely because an older migration note introduced them as one option.

## 0.9.0 Development Velocity migration

When migrating from 0.8.x to 0.9.0, the goal is to remove **validation waterfall and coordination ceremony** without weakening integration/release confidence.

### 1. Upgrade operating/execution contracts

Merge:

- `.engineering/commands.json` operating contract `0.6.0`;
- `EXECUTION-CAPABILITY-CONTRACT.md` `0.3.0` semantics;
- explicit delivery stages `ITERATION`, `INTEGRATION`, `RELEASE`;
- publication gate beginning at `INTEGRATION` rather than every implementation edit/draft update.

`ITERATION` should stay fast and focused. `INTEGRATION` owns exact-head/base, complete-diff review, affected durable-doc freshness and integration evidence. `RELEASE` owns full release-grade confidence.

Do not interpret the migration as permission to skip required integration/release evidence.

### 2. Separate stage from validation depth

Keep `LEAN`, `SCOPED`, `STRONG`, `FULL`, but stop treating them as lifecycle states.

Adapt the project selector so it reports:

- changed owners;
- risk dimensions;
- concrete required gates;
- profile/reason.

Prefer gate selection over broad suite selection.

Preserve a stronger existing dependency/scope detector rather than adding a second selector.

Unknown executable scope should fail safe stronger. Changes to selector/global build/toolchain/dependency inventory that control narrowing should validate `FULL`.

### 3. Reuse equivalent successful evidence

Update `preflight-change`/`remote-preflight` and repository automation so existing successful evidence is reused when it remains sufficient for:

- exact source head;
- material target/base relationship;
- required gates;
- selected profile or stronger equivalent;
- E2E environment/fidelity/evidence mode when applicable.

A replacement PR, draft -> ready transition, label/comment or another collaboration-only change must not force an expensive rerun by itself.

Rerun only missing, stale or insufficient gates.

Security remains unchanged: exact-head pinning, trusted requesters, least privilege and no production/signing/deployment secrets in change-branch execution.

### 4. Upgrade E2E contract to 0.2.0

0.9.0 supersedes the 0.8.1 unconditional UI-media rule.

Do **not** delete useful screenshot/video tooling. Change the requirement routing to risk-based modes:

- `ASSERTIONS` — UI incidental to deterministic non-visual behavior;
- `SCREENSHOTS` — stable visible states/layout/hierarchy/copy/recovery/adaptive semantics;
- `FULL_MEDIA` — motion/timing/progression/navigation transitions/lifecycle visibility/gesture continuity/release acceptance.

Migrate `.engineering/e2e.json` to contract `0.2.0` and declare each journey's minimum UI evidence mode.

Evidence required by the selected mode must remain identity-bearing, privacy-safe and bounded-retention. Missing required evidence stays `E2E_EVIDENCE_INCOMPLETE`.

Do not downgrade an evidence mode after a run merely to obtain PASS.

### 5. Migrate validation/preflight Skills

Merge 0.9.0 semantics into:

- `validate-change` — fast `ITERATION`, risk-to-gate expansion, proportional E2E;
- `preflight-change` — begins at `INTEGRATION`/`RELEASE`, reuses evidence before triggering work;
- `remote-preflight` — satisfies only missing/stale/insufficient remote gates;
- `structured-change` — proportional reasoning, no publication ceremony on every edit;
- `plan-workstream` — observable vertical outcomes and early convergence.

Preserve project-local customizations that encode real product/platform behavior.

### 6. Migrate workstream/branch strategy

Parallel development does not imply stacked publication.

Prefer temporary independent branches/worktrees that converge early into the same coherent vertical outcome.

Retain stacked PRs only when each level is independently mergeable/reviewable/value-bearing or separate ownership requires it.

Treat repeated sync-only parent/child PRs as coordination debt to eliminate, not normal evidence ceremony.

### 7. Migrate documentation timing

During `ITERATION`, affected durable docs may remain pending while implementation changes.

Before `INTEGRATION`, every affected canonical documentation owner must be current with the exact candidate.

Keep `docs/current-state.md` as integrated/blocked/next repository truth. Do not churn it for every agent commit/branch sync.

Completed plans remain delete-by-default after durable knowledge transfer.

### 8. Migrate PR evidence

Ordinary integration PRs should be concise:

- observable outcome;
- scope/risks;
- concrete changes;
- stage/profile/required gates;
- reused/new validation evidence;
- E2E only when applicable;
- affected durable docs;
- remaining real-environment gaps.

Use a separate release template/checklist for `RELEASE` rather than turning every feature PR into a release dossier.

### 9. Migrate stack profiles

For Android in particular:

- iteration should prefer affected compile/focused unit/direct-contract checks;
- broad AndroidTest assembly, emulator, minified/release packaging, native packaging and media capture should be triggered by concrete integration/release risk rather than every edit;
- Binder/public contracts, persistence, lifecycle, native/JNI, manifest/R8/package/variant and complete journeys retain appropriate stronger gates;
- physical/OEM evidence remains residual for claims automation cannot faithfully prove.

Apply equivalent native specialization for other adopted profiles.

### 10. Add validation economics

Where practical, start reviewing expensive gates for:

- duration;
- flake rate;
- unique regression signal;
- overlap.

Use this to change **placement and scope**, not to delete meaningful safety evidence.

The target is sufficient confidence per feedback time.

## 0.9.2 Release-only real-environment migration

When migrating from 0.9.1 to 0.9.2, the goal is to make the stage boundary unambiguous: **integration proves the feature automatically; release proves the residual real-environment delta**.

### 1. Upgrade machine-readable contracts

Migrate:

- `.engineering/commands.json` to operating contract `0.6.1`;
- `.engineering/e2e.json` to E2E contract `0.2.1`;
- `EXECUTION-CAPABILITY-CONTRACT.md` `0.3.2` semantics;
- `preflight-change` to source version `0.9.2`.

Do not bump `.engineering/baseline.json` until these semantics are actually implemented.

### 2. Make integration automated and real-environment non-blocking

In `development_velocity.integration`, preserve exact-head/diff/docs/risk-gate readiness and add:

- `automated_e2e_required_when_affected: true`;
- `real_environment_blocking: false`;
- `real_environment_deferred_to_release: true`.

Affected complete critical journeys must pass automatically before the coherent slice enters the shared development/integration branch when lower-level tests cannot prove the outcome.

A missing local SDK does not change this into user-run testing; use repository-owned remote automation.

### 3. Make required real-environment evidence blocking at release

In `development_velocity.release`, require `required_real_environment_blocking: true`.

`AUTOMATED_PREFLIGHT_CONFIRMED` is an integration-readiness claim. `RELEASE_READY` additionally requires every applicable real-environment confirmation required by the release claim to pass.

Do not present `DEFERRED_TO_RELEASE` evidence as already passed.

### 4. Add E2E stage policy

In `.engineering/e2e.json`, add `stage_policy` so the project explicitly records:

- automated E2E before shared integration;
- non-blocking real environment at integration;
- real-environment deferral to release;
- required real environment blocking at release.

Keep target environments, execution environments, critical journeys and residual gaps truthful to the project.

### 5. Strengthen material UI/UX integration evidence

Keep UI evidence risk-based, but make the stage rule explicit:

- `ASSERTIONS` remains valid when UI is only an incidental harness for a non-visual system invariant;
- `SCREENSHOTS` remains valid for bounded stable-state inspection when the complete UI/UX journey itself is not the material integration claim;
- `FULL_MEDIA` is the default for a **material UI/UX critical journey entering the shared development branch** and includes bounded screenshots plus one continuous journey video.

This is not a return to the 0.8.1 rule that every UI-bearing test needs video.

### 6. Add deterministic policy enforcement

Adopt `scripts/verify_stage_environment_policy.py` and wire it into repository health alongside `verify_operations.py` and `verify_e2e.py`.

The existing operation/E2E verifiers should also enforce contract versions `0.6.1` and `0.2.1` plus the new stage fields.

### 7. Specialize platform profiles

For Android, the normal integration path is:

```text
focused lower-level gates
-> emulator/instrumentation or built-APK automated E2E
-> screenshot + video when UI/UX is materially part of the journey
-> shared development branch
```

Release then closes applicable physical/OEM/device-specific gaps such as real memory pressure, thermals, native backend/ABI behavior or OEM lifecycle differences.

An early physical-device run remains valid for diagnosis of an explicitly hardware-specific defect; it does not become the default branch/PR integration blocker.

## Migration validation

A migration is not complete until:

- machine-readable contracts pass project verifiers;
- relevant local Skills/guides no longer impose superseded stage/media behavior;
- risk selector/remote-preflight behavior is coherent with existing CI;
- affected project-specific validation passes;
- E2E environment/evidence semantics are truthful;
- baseline metadata reports real behavior, not intent.

For a repository whose validation/selector/CI machinery changes during migration, use `FULL` validation for the migration itself because the narrowing mechanism is part of the changed scope.

## Output

Report:

```text
BASELINE: <old> -> <new>
DELTAS:
  APPLY: <items>
  MERGE: <items>
  N/A: <items>
  DEFER: <items>
  CONFLICT: <items>
LOCAL_CUSTOMIZATIONS_PRESERVED: <items>
DELIVERY_MODEL: <iteration/integration/release specialization>
RISK_TO_GATE_SELECTOR: <strategy>
REMOTE_PREFLIGHT_AND_REUSE: <strategy>
E2E_ENVIRONMENT_AND_UI_EVIDENCE: <journeys/fidelity/modes/residual gaps>
VALIDATION_ECONOMICS: <implemented/deferred>
MIGRATION_VALIDATION: <evidence>
```
