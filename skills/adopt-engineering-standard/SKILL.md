---
name: adopt-engineering-standard
description: Align a new or existing repository with repo-template-sw without blindly overwriting stronger project-specific architecture, CI, documentation, operating commands, E2E tooling, design systems or agent guidance. Audit first, select applicable profiles, create a bounded adoption DAG, then install/specialize the smallest useful baseline.
---

# Adopt Engineering Standard

## Goal

Make a repository self-contained and aligned with the Agent-Native Reference Engineering Standard while preserving good existing engineering and product-experience decisions.

## New repository path

1. Identify product/runtime, languages/platforms, persistence/network/security boundaries, build/distribution shape, expected deployment environment and whether a material user-facing UI exists.
2. Copy the universal `template/` baseline.
3. Select only applicable profiles from `profiles/`. Add `product-ui` only when the repository has a material interface.
4. Specialize `AGENTS.md`, `docs/architecture.md`, `SECURITY.md`, ownership maps and `.engineering/commands.json`. Remove unresolved placeholders before calling adoption complete.
5. Map common command intents (`setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop`, `clean`) to native tooling; mark only genuinely inapplicable intents `n/a`.
6. Decide E2E applicability explicitly. Add a small set of critical complete-workflow tests when lower-level tests cannot establish the full outcome; preserve stack-native tooling and prefer Playwright only for browser/web when no equally strong incumbent exists.
7. Implement applicable project operating contracts: unique build identity, artifact lineage/retention/manifest/checksum, generated build delta, localhost/runtime cleanup and ephemeral-resource cleanup.
8. If `product-ui` is selected, specialize `design/ux-contract.json` and `design/brand-kit.json`: declare design source of truth, task/IA hierarchy, progressive disclosure/defaults, critical states/journeys, accessibility, adaptive layout, semantic design-system ownership, key reference views and applicable UX regression evidence.
9. Record standard version, profiles and local Skill customization in `.engineering/baseline.json`.
10. Configure stack-specific formatter/lint/static/test/E2E/build/smoke CI plus applicable accessibility/visual/UI evidence gates and branch protection.
11. Run repository/operating/product-experience/docs/agent-context validation.
12. Report current maturity truthfully; bootstrap alone establishes structure, not L1/L2 evidence.

## Existing repository path

### 1. Discover before copying

Inspect:

- existing `AGENTS.md`/agent instructions and Skills;
- README/architecture/ADRs/current plans;
- current setup/dev/check/test/E2E/build/package/clean commands and scripts;
- existing E2E framework, critical journeys and failure evidence/retention;
- CI, branch/release policy and package/build configuration;
- build/version naming, artifact storage/retention and release flow;
- local servers, ports, helper processes, PID/lock/temp state and shutdown paths;
- tests/integration/device evidence;
- security/privacy/data lifecycle;
- resource/memory/concurrency ownership;
- repository/generated-artifact hygiene;
- when a material UI exists: information architecture, progressive disclosure, defaults/action hierarchy, critical states, accessibility, adaptive layout, existing design/brand source of truth, tokens/components, key mockups/reference views and usability/visual regression evidence.

### 2. Classify existing practices

For each baseline concern mark:

- `KEEP` — existing project mechanism is equal/stronger;
- `ADAPT` — align routing/contracts without losing local value;
- `ADD` — real gap;
- `N/A` — not applicable;
- `CONFLICT` — existing practice contradicts a required invariant and needs an explicit decision.

Never replace a stronger existing mechanism merely to make repositories look identical. Common contracts standardize semantics, not the underlying build/E2E/design tool or visual style.

### 3. Build an adoption DAG

Create a temporary workstream only when migration spans multiple coordinated changes. Prioritize:

- safety/ownership/conflict resolution;
- canonical command and agent routing;
- deterministic validation/CI;
- critical-workflow E2E gaps where lower-level tests are insufficient;
- build/artifact identity and release/retention gaps;
- runtime/process/port/ephemeral cleanup gaps;
- if `product-ui`: task/IA/accessibility/critical-state/design-system gaps that materially affect usability/correctness;
- resource/failure/data gaps;
- cleanup/duplicate plan/mockup removal.

Expose dependencies and non-conflicting parallel lanes.

### 4. Install/specialize

Copy only missing/useful universal files and core Skills. Merge project-specific `AGENTS.md`, CI, SECURITY, docs and design contracts semantically.

Preserve native Gradle/Xcode/Python/Node/etc. workflows, strong E2E suites and established design systems/Figma/code-first sources. Do not replace them merely to resemble the template.

For existing artifact/build systems, migrate identity/retention/delta behavior incrementally. For existing UI systems, use `design/ux-contract.json` and `design/brand-kit.json` as routing/contract metadata pointing to real owners; do not create a second design truth.

### 5. Validate and finalize

Run project tests plus baseline health checks. For `product-ui`, run `verify_product_experience.py` and the UI evidence relevant to the adoption claim. For full-workflow changes run applicable `e2e`; for runtime/build migrations execute an applicable `build`/`smoke`/`stop` cycle and verify no project-owned process/listener/browser/device/temp residue remains.

Transfer durable changes, delete adoption workstream by default, and leave the repository self-contained.

## Non-goals

- forcing identical layouts, build tools, E2E frameworks, design tools or visual styles;
- introducing wrappers solely to normalize command names;
- introducing Playwright where no browser E2E boundary exists or an equally strong incumbent already works;
- introducing a UI/design framework solely for compliance aesthetics;
- creating duplicate Figma/code/mockup sources of truth;
- claiming production/reference readiness without evidence;
- keeping the template repository as a runtime dependency.
