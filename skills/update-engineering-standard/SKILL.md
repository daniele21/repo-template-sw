---
name: update-engineering-standard
description: Migrate an already-adopted repository from its recorded repo-template-sw baseline to a newer version through an explicit semantic delta review that preserves local customizations and applies only relevant changes.
---

# Update Engineering Standard

## Principle

An adopted repository is self-contained. Standard updates are explicit migrations, not automatic synchronization.

## Workflow

1. Read the project's `.engineering/baseline.json` and current local Skills/guides.
2. Read `VERSION` and relevant `CHANGELOG.md` entries in `repo-template-sw` from the recorded version to the target version.
3. Classify each delta:
   - `APPLY` — directly relevant and local copy is unmodified;
   - `MERGE` — relevant but local file/Skill is customized;
   - `N/A` — profile/concern not used by the project;
   - `DEFER` — valid change intentionally postponed with a named reason/owner;
   - `CONFLICT` — requires an explicit architecture/product decision.
4. Inspect local behavior before replacing text. Never overwrite a customized Skill or project-specific `AGENTS.md` wholesale.
5. Implement the smallest migration that establishes the new invariant/behavior.
6. Run baseline health checks plus project-specific validation affected by the migration.
7. Update `.engineering/baseline.json` source version and per-Skill `source_version`; preserve `customized: true` where local divergence remains intentional.
8. Update durable project docs only when current behavior/ownership changed.
9. If a migration workstream was required, finalize and delete it by default.

## Output

Report:

- old -> new baseline version;
- deltas applied/merged/deferred/not applicable;
- local customizations preserved;
- validation executed;
- unresolved conflicts/deferred migrations.

A version bump without applying or explicitly classifying relevant semantic deltas is not a valid migration.
