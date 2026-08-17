---
name: update-engineering-standard
description: Migrate an already-adopted repository from its recorded repo-template-sw baseline to a newer version through an explicit semantic delta review that preserves local customizations and applies only relevant changes.
---

# Update Engineering Standard

## Principle

An adopted repository is self-contained. Standard updates are explicit migrations, not automatic synchronization.

## Workflow

1. Read the project's `.engineering/baseline.json`, `.engineering/commands.json` when present, and current local Skills/guides.
2. Read `VERSION`, `CHANGELOG.md`, `STANDARD.md` and any changed focused contract documents in `repo-template-sw` from the recorded version to the target version.
3. Classify each delta:
   - `APPLY` — directly relevant and local copy is unmodified;
   - `MERGE` — relevant but local file/Skill/operating mechanism is customized;
   - `N/A` — profile/concern not used by the project;
   - `DEFER` — valid change intentionally postponed with a named reason/owner;
   - `CONFLICT` — requires an explicit architecture/product decision.
4. Inspect local behavior before replacing text. Never overwrite a customized Skill, project-specific `AGENTS.md`, native build tooling, E2E framework or release flow wholesale.
5. For operating-contract changes, map semantics onto existing native commands before adding wrappers. Preserve stronger local build/runtime/artifact/E2E mechanisms.
6. Implement the smallest migration that establishes the new invariant/behavior.
7. Run baseline health checks plus project-specific validation affected by the migration. When complete-workflow behavior changed, run applicable E2E; when build/runtime lifecycle changed, run an applicable build/smoke/stop cycle and verify post-clean state.
8. Update `.engineering/baseline.json` source version and per-Skill `source_version`; preserve `customized: true` where local divergence remains intentional.
9. Update `.engineering/commands.json` contract version and local command/policy mappings only after the corresponding behavior is real.
10. Update durable project docs only when current behavior/ownership changed.
11. If a migration workstream was required, finalize and delete it by default.

## 0.2 operating-contract migration guidance

When migrating from 0.1.x to 0.2.x, explicitly classify:

- canonical command intents and existing scripts/tasks;
- unique build identity and artifact naming;
- artifact lineage, manifest/checksum, local retention and CI/release storage;
- generated build delta against the previous successful comparable build;
- localhost/process/port ownership and shutdown verification;
- temporary files, locks, test stores, logs, caches and stale-run recovery;
- operating-contract CI enforcement.

Do not claim 0.2 adoption by only copying `.engineering/commands.json` or bumping metadata.

## 0.3 E2E migration guidance

When migrating from 0.2.x to 0.3.x, explicitly classify:

- whether the project has critical complete workflows whose outcome is not adequately proven by unit/integration/contract tests;
- the new canonical `e2e` command mapping, or a truthful `n/a` when no meaningful E2E boundary exists;
- existing E2E framework/tooling that should be `KEEP` rather than replaced;
- a small critical-journey set instead of broad brittle UI automation;
- built/package artifact execution when the product claim depends on it;
- E2E cleanup of servers, browser/device sessions, test data, downloads and temporary state;
- failure trace/screenshot/video/log handling with identity and bounded retention;
- CI routing so E2E runs at an appropriate cadence rather than unnecessarily slowing every local edit.

For browser/web projects adding a new E2E framework, prefer Playwright unless an equally strong established solution already exists. Do not add Playwright to server/native projects merely to satisfy the baseline.

A metadata-only 0.3 bump without classifying E2E applicability is not a valid migration.

## Output

Report:

- old -> new baseline version;
- deltas applied/merged/deferred/not applicable;
- local customizations preserved;
- operating-contract/E2E mappings and migrations;
- validation executed;
- unresolved conflicts/deferred migrations.

A version bump without applying or explicitly classifying relevant semantic deltas is not a valid migration.
