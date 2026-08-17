---
name: adopt-engineering-standard
description: Align a new or existing repository with repo-template-sw without blindly overwriting stronger project-specific architecture, CI, documentation, operating commands, E2E tooling or agent guidance. Audit first, select applicable profiles, create a bounded adoption DAG, then install/specialize the smallest useful baseline.
---

# Adopt Engineering Standard

## Goal

Make a repository self-contained and aligned with the Agent-Native Reference Engineering Standard while preserving good existing engineering decisions.

## New repository path

1. Identify product/runtime, languages/platforms, persistence/network/security boundaries, build/distribution shape and expected deployment environment.
2. Copy the universal `template/` baseline.
3. Select only applicable profiles from `profiles/` and adapt their requirements into project-local configuration/agent guidance.
4. Specialize `AGENTS.md`, `docs/architecture.md`, `SECURITY.md`, ownership maps and `.engineering/commands.json`. Remove all unresolved placeholders before calling adoption complete.
5. Map the common command intents (`setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop`, `clean`) to the repository's native tooling; mark only genuinely inapplicable intents `n/a`.
6. Decide E2E applicability explicitly. Add a small set of critical complete-workflow tests when lower-level tests cannot establish the full outcome; preserve stack-native tooling and prefer Playwright only for browser/web when no equally strong incumbent exists.
7. Implement the applicable project operating contracts: unique build identity, artifact lineage/retention/manifest/checksum, generated build delta, localhost/runtime cleanup and ephemeral-resource cleanup.
8. Record standard version, profiles and local Skill customization in `.engineering/baseline.json`.
9. Configure stack-specific formatter/lint/static/test/E2E/build/smoke CI gates according to the product's real critical paths and branch protection.
10. Run repository/operating-contract/docs/agent-context validation.
11. Report current maturity truthfully; bootstrap alone normally establishes structure, not L1/L2 evidence.

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
- repository/generated-artifact hygiene.

### 2. Classify existing practices

For each baseline concern mark:

- `KEEP` — existing project mechanism is equal/stronger;
- `ADAPT` — align naming/routing/tooling without losing local value;
- `ADD` — real gap;
- `N/A` — not applicable to this project;
- `CONFLICT` — existing practice contradicts a required invariant and needs an explicit decision.

Never replace a stronger existing mechanism merely to make repositories look identical. The command contract standardizes semantics, not the underlying build or E2E tool.

### 3. Build an adoption DAG

Create a temporary workstream only when migration spans multiple coordinated changes. Prioritize:

- safety/ownership/conflict resolution;
- canonical command routing and agent guidance;
- deterministic validation/CI;
- critical-workflow E2E gaps where lower-level tests are insufficient;
- build/artifact identity and release/retention gaps;
- runtime/process/port/ephemeral cleanup gaps;
- resource/failure/data gaps;
- cleanup/duplicate plan removal.

Expose dependencies and non-conflicting parallel lanes.

### 4. Install/specialize

Copy only missing/useful universal files and core Skills. Merge project-specific `AGENTS.md`, CI, SECURITY and docs semantically. Preserve native Gradle/Xcode/Python/Node/etc. workflows and existing strong E2E suites behind the common command intents rather than wrapping/replacing them unnecessarily.

For existing artifact/build systems, migrate identity/retention/delta behavior incrementally. Do not invalidate or delete historical release artifacts merely to conform to the new layout.

### 5. Validate and finalize

Run project tests plus baseline health and operating-contract checks. For full-workflow changes, run the applicable `e2e`; for runtime/build migrations, execute an applicable real `build`/`smoke`/`stop` cycle and verify no project-owned process/listener/browser/device/temp residue remains. Transfer durable changes, delete adoption workstream by default, and leave the repository self-contained.

## Non-goals

- forcing identical folder layouts, build tools or E2E frameworks across unrelated stacks;
- introducing Make/Docker/Python wrappers solely to normalize command names;
- introducing Playwright where no browser E2E boundary exists or an equally strong incumbent already works;
- introducing frameworks/services/dependencies solely for compliance aesthetics;
- claiming production/reference readiness without evidence;
- keeping the template repository as a runtime dependency.
