---
name: adopt-engineering-standard
description: Align a new or existing repository with repo-template-sw 0.9.2 without overwriting stronger project-specific architecture, CI, documentation, build/E2E tooling, design systems or agent guidance. Audit first, then specialize staged delivery, risk-based validation and the smallest useful baseline.
---

# Adopt Engineering Standard

## Goal

Make a repository self-contained and aligned with the Agent-Native Reference Engineering Standard while preserving good existing engineering/product decisions.

Adoption is semantic. Do not call a repository 0.9.2-compliant merely because files or version metadata were copied.

## 1. Discover before changing

Inspect the current repository for:

- product/runtime/platforms and public boundaries;
- root/scoped agent guidance and existing Skills;
- architecture/ADRs/current plans and documentation ownership;
- setup/dev/check/test/E2E/build/package/clean commands;
- CI scope selection and typical validation latency;
- remote automation/preflight mechanisms;
- test suites and what risk each materially proves;
- E2E framework, critical journeys, target/execution environments and residual physical/manual tests;
- existing screenshot/video capture and evidence retention;
- build identity/artifact/release mechanisms;
- persistence/security/resource/lifecycle ownership;
- design source of truth and UX/UI system when material.

For an existing repository classify mechanisms as:

- `KEEP` — already satisfies/exceeds the invariant;
- `ADAPT` — keep mechanism, add missing semantics/routing;
- `ADD` — genuinely missing capability;
- `N/A` — concern does not apply.

Never replace a stronger incumbent framework merely for template uniformity.

## 2. Install/specialize the baseline

For a new repository, copy the universal `template/` and then specialize it.

For an existing repository, merge only the relevant files/semantics.

Select only applicable profiles. Add `product-ui` only when a material user-facing interface exists.

Remove unresolved adopter placeholders before claiming adoption complete.

## 3. Map project commands

In `.engineering/commands.json`, map canonical intents to native tooling:

`setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop`, `clean`.

Mark only genuinely irrelevant intents `n/a`.

Do not introduce a wrapper framework solely for naming consistency.

Use operating contract `0.6.1` and preserve its integration/release real-environment stage fields.

## 4. Specialize the 0.9.2 development-velocity model

Preserve the three delivery stages:

### `ITERATION`

- default while implementation changes;
- fastest useful formatter/static/compile/focused-test/direct-contract feedback;
- no automatic exact-head/full-diff/docs/preflight/release-E2E ceremony.

### `INTEGRATION`

- coherent observable vertical outcome ready for the shared development/integration branch;
- exact head/base;
- complete diff review;
- affected durable docs current;
- concrete risk gates satisfied;
- affected complete critical journeys proven automatically when lower-level evidence is insufficient;
- residual `REAL_ENVIRONMENT` evidence explicit and `DEFERRED_TO_RELEASE`, not a normal integration blocker.

### `RELEASE`

- release/promotion/reference checkpoint;
- `FULL` validation and release-critical artifact/E2E evidence;
- every real-environment confirmation required by the release claim passes before `RELEASE_READY`.

Keep delivery stage separate from validation depth.

If repository-specific feedback-time budgets make sense, specialize the reference iteration/integration targets without turning them into correctness-breaking hard timeouts.

## 5. Build risk-to-gate selection

Prefer the repository's existing dependency/ownership graph when available.

The selector should produce:

- changed owners;
- risk dimensions;
- concrete required gates;
- `LEAN | SCOPED | STRONG | FULL` summary/reason.

Typical escalation risks:

- shared/public contract;
- persistence/migration;
- security/trust/data lifecycle;
- runtime/resource/concurrency/lifecycle;
- native/JNI/backend;
- manifest/dependency/variant/package/R8;
- complete user/system journey;
- selector/global build/toolchain/dependency-inventory changes.

Do not map an entire important feature area to `STRONG/FULL` without a changed invariant that requires those gates.

Unknown executable scope fails safe stronger. Selector/global-build machinery that controls narrowing validates `FULL` when it changes.

## 6. Configure execution capability and remote preflight

Required gates are classified as:

- `AGENT_LOCAL`;
- `REMOTE_AUTOMATED`;
- `REAL_ENVIRONMENT`.

Do not delegate ordinary automatable compile/lint/test/R8/package/emulator work to the user because the current agent lacks tooling.

When agents may lack a local environment, provide repository-owned remote automation with least privilege.

Configure evidence reuse so successful existing results can satisfy integration/release preflight when they still match exact source head, material base relationship, required gates/profile and E2E environment/evidence mode.

PR number/draft/ready/label/comment identity must not independently force duplicate validation.

Execution class and stage placement are separate: classify residual real-environment requirements during integration, but execute/block on required ones at release by default.

## 7. Configure E2E environments, stage policy and journeys

Decide E2E applicability explicitly.

When applicable, `.engineering/e2e.json` contract `0.2.1` declares:

- target environments and material dimensions;
- automated execution environments and fidelity classes;
- integration/release `stage_policy`;
- bounded high-value critical journeys;
- minimum automated fidelity;
- known/residual fidelity gaps;
- real-environment confirmation policy;
- minimum UI evidence mode.

Preserve existing Compose/Espresso/UI Automator/XCUITest/Playwright/API/CLI/device-farm tooling when strong.

At integration, use the cheapest sufficient automated environment to prove the complete changed outcome. Carry only residual physical/target-specific gaps to release.

An early physical/target run may still be useful for diagnosing an explicitly environment-specific defect without becoming the standard branch/PR integration gate.

## 8. Configure risk-based UI E2E evidence

0.9.2 does **not** return to the old rule that every journey touching UI needs video.

Use:

- `ASSERTIONS` — UI incidental to non-visual deterministic behavior;
- `SCREENSHOTS` — bounded stable visible states/layout/hierarchy/copy/recovery/adaptive semantics;
- `FULL_MEDIA` — screenshots plus continuous journey video when UI/UX is materially part of the integration outcome, or when motion/timing/progression/navigation transitions/lifecycle visibility/gesture continuity/release acceptance matters.

A material UI/UX critical journey entering the shared development branch uses `FULL_MEDIA` by default.

Preserve existing screenshot/video infrastructure where useful. Route it to the modes/stages that need it rather than deleting it.

Evidence required by the selected mode must be identity-bearing, privacy-safe and bounded-retention. Missing required evidence is `E2E_EVIDENCE_INCOMPLETE`.

## 9. Configure vertical work and parallelism

`plan-workstream` is used only when persistent dependency/parallel coordination adds value.

Prefer observable vertical outcomes. Treat technical layers as subtasks unless independently useful/mergeable/reviewable.

Parallel work may use branches/worktrees, but related work should converge early onto a shared feature/integration outcome.

Stacked publication is exception-only. Do not design a workflow that requires repeated sync-only PRs for normal agent parallelism.

## 10. Configure documentation lifecycle

Keep:

- bounded `AGENTS.md` routing;
- durable architecture/features/ADRs;
- `docs/current-state.md` as integrated/blocked/next truth;
- active bounded workstreams only;
- completed workstreams deleted after durable knowledge transfer by default.

During `ITERATION`, affected durable docs may be pending while behavior changes. At `INTEGRATION`, affected canonical docs must be current.

Do not churn current-state/workstream metadata for every temporary branch synchronization.

## 11. Configure operating/build/resource invariants

Implement applicable:

- unique build identity;
- immutable successful artifacts;
- manifest/checksum/build delta;
- bounded local/CI evidence retention;
- graceful runtime shutdown;
- isolated run resources;
- cleanup after success/failure/timeout/cancellation/interrupt/partial initialization.

Preserve stronger existing artifact/release systems.

## 12. Product experience

If `product-ui` applies, specialize `design/ux-contract.json` and `design/brand-kit.json` around actual users/jobs/surfaces and the real design-system source of truth.

Preserve the proportional decision order:

```text
user outcome -> task -> IA/journey -> hierarchy/disclosure/defaults
-> interaction/states/feedback/recovery -> adaptive/platform -> accessibility
-> design system -> motion -> visual/graphics -> validation
```

Do not redesign a mature product merely to adopt the baseline.

## 13. Validation economics

Where practical, identify expensive gates and begin collecting/reviewing:

- duration;
- flake rate;
- unique regression signal;
- overlap.

Do not delete real safety evidence for speed. Use the signal to improve where gates run: cheap/focused evidence in iteration, affected automated E2E in integration and real-environment acceptance in release.

## 14. Finalize adoption

Run applicable repository/operations/E2E/stage-policy/product-experience/docs/context verifiers and project-specific validation needed by the adoption itself.

Only then update `.engineering/baseline.json` to `0.9.2` and record local Skill customization truthfully.

Report:

```text
BASELINE: 0.9.2
PROFILES: <list>
KEEP: <strong existing mechanisms preserved>
ADAPT: <mechanisms merged with new semantics>
ADD: <new capabilities>
N/A: <non-applicable concerns>
DELIVERY_MODEL: ITERATION / INTEGRATION / RELEASE <specialization>
RISK_SELECTOR: <owner/risk/gate strategy>
REMOTE_PREFLIGHT: <trigger + evidence reuse strategy>
E2E: <journeys/environments/stage policy/residual gaps/UI evidence modes>
VALIDATION_ECONOMICS: <implemented/deferred>
VALIDATION: <evidence>
DEFERRED_OR_CONFLICTS: <items or N/A>
```

Bootstrap structure alone does not prove L1/L2 maturity. Report maturity truthfully from actual evidence.
