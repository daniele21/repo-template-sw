---
name: adopt-engineering-standard
description: Align a new or existing repository with repo-template-sw without blindly overwriting stronger project-specific architecture, CI, documentation or agent guidance. Audit first, select applicable profiles, create a bounded adoption DAG, then install/specialize the smallest useful baseline.
---

# Adopt Engineering Standard

## Goal

Make a repository self-contained and aligned with the Agent-Native Reference Engineering Standard while preserving good existing engineering decisions.

## New repository path

1. Identify product/runtime, languages/platforms, persistence/network/security boundaries and expected deployment environment.
2. Copy the universal `template/` baseline.
3. Select only applicable profiles from `profiles/` and adapt their requirements into project-local configuration/agent guidance.
4. Specialize `AGENTS.md`, `docs/architecture.md`, `SECURITY.md`, validation commands and ownership maps. Remove all unresolved placeholders before calling adoption complete.
5. Record standard version, profiles and local Skill customization in `.engineering/baseline.json`.
6. Configure stack-specific formatter/lint/static/test/build CI gates and branch protection.
7. Run repository/docs/agent-context validation.
8. Report current maturity truthfully; bootstrap alone normally establishes structure, not L1/L2 evidence.

## Existing repository path

### 1. Discover before copying

Inspect:

- existing `AGENTS.md`/agent instructions and Skills;
- README/architecture/ADRs/current plans;
- CI, branch/release policy and package/build configuration;
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

Never replace a stronger existing mechanism merely to make repositories look identical.

### 3. Build an adoption DAG

Create a temporary workstream only when migration spans multiple coordinated changes. Prioritize:

- safety/ownership/conflict resolution;
- agent routing and canonical docs;
- deterministic validation/CI;
- resource/failure/data gaps;
- cleanup/duplicate plan removal.

Expose dependencies and non-conflicting parallel lanes.

### 4. Install/specialize

Copy only missing/useful universal files and core Skills. Merge project-specific `AGENTS.md`, CI, SECURITY and docs semantically. Add scoped guides only where local complexity justifies them.

### 5. Validate and finalize

Run project tests plus baseline health checks. Transfer durable changes, delete adoption workstream by default, and leave the repository self-contained.

## Non-goals

- forcing identical folder layouts across unrelated stacks;
- introducing frameworks/services/dependencies solely for compliance aesthetics;
- claiming production/reference readiness without evidence;
- keeping the template repository as a runtime dependency.
