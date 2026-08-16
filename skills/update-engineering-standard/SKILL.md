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
4. Inspect local behavior before replacing text. Never overwrite a customized Skill, project-specific `AGENTS.md`, native build tooling or release flow wholesale.
5. For operating-contract changes, map semantics onto existing native commands before adding wrappers. Preserve stronger local build/runtime/artifact mechanisms.
6. Implement the smallest migration that establishes the new invariant/behavior.
7. Run baseline health checks plus project-specific validation affected by the migration. When build/runtime lifecycle changed, run an applicable build/smoke/stop cycle and verify post-clean state.
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

## Output

Report:

- old -> new baseline version;
- deltas applied/merged/deferred/not applicable;
- local customizations preserved;
- operating-contract mappings/migrations;
- validation executed;
- unresolved conflicts/deferred migrations.

A version bump without applying or explicitly classifying relevant semantic deltas is not a valid migration.
