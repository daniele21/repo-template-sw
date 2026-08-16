# repo-template-sw

Agent-native reference engineering baseline for software repositories maintained by humans and coding agents.

`repo-template-sw` is not an application framework and is not intended to become a universal build system. It is the canonical source for a small engineering standard, a reusable project bootstrap, a common project operating contract, coding-agent Skills, documentation/context governance, and deterministic repository health checks.

## Start here

- [`USAGE.md`](USAGE.md) — practical guide for using this repository with a brand-new project, an existing repository, ordinary coding-agent development, audits and baseline upgrades.
- [`STANDARD.md`](STANDARD.md) — canonical L0/L1/L2 engineering standard.
- [`OPERATING-CONTRACT.md`](OPERATING-CONTRACT.md) — common stack-neutral semantics for setup/dev/test/build/smoke/package/cleanup, build identity, artifacts and local runtimes.

## What it optimizes for

- software correctness and explicit ownership;
- bounded memory/resources, concurrency and failure behavior;
- reproducible builds and validation;
- consistent project operations without forcing identical tooling;
- uniquely identifiable builds and traceable immutable artifacts;
- bounded artifact/cache/log retention and zero-residue runtime/build lifecycles;
- privacy/security and data lifecycle clarity;
- low repository/documentation entropy;
- low coding-agent context and token cost;
- safe parallel work through dependency-aware workstreams;
- reusable operating procedures without stuffing `AGENTS.md` with every rule.

The design principles are:

> **Make ownership, limits, failures and costs explicit, using the simplest solution that preserves the required invariants.**

> **Every operation must be identifiable, owned, bounded, reversible and leave no unintended residue.**

## Repository layout

- [`STANDARD.md`](STANDARD.md) — canonical L0/L1/L2 engineering standard.
- [`OPERATING-CONTRACT.md`](OPERATING-CONTRACT.md) — normative project command/build/artifact/runtime lifecycle semantics.
- [`USAGE.md`](USAGE.md) — practical adoption and operating guide.
- [`template/`](template/) — universal files that can be adopted into a project and then specialized locally.
- [`template/.engineering/commands.json`](template/.engineering/commands.json) — machine-readable project operating contract template.
- [`template/skills/`](template/skills/) — core project-local coding-agent Skills.
- [`profiles/`](profiles/) — optional stack/domain guidance; profiles map native tooling to common semantics and add only genuinely specific requirements.
- [`skills/adopt-engineering-standard/`](skills/adopt-engineering-standard/) — workflow for aligning a new/existing repository.
- [`skills/update-engineering-standard/`](skills/update-engineering-standard/) — workflow for migrating an adopted repository to a newer baseline.

## Core model

```text
AGENTS.md          -> how to orient and what is invariant
commands.json      -> how this project sets up/runs/checks/tests/builds/cleans
Skills             -> how to perform recurring change workflows
Active workstream  -> what is being implemented now
Feature/ADR/docs   -> how the system works now and why durable decisions exist
Git history        -> how the repository got here
Scripts/CI         -> deterministic enforcement
```

The operating model is **same semantics, native implementation**. Android can remain Gradle-native, macOS Xcode/Swift/Python-native, and local servers use their established Python/Node/etc. tooling while exposing the same conceptual command intents.

## Use with a new project

1. Read [`USAGE.md`](USAGE.md), `STANDARD.md` and the applicable parts of `OPERATING-CONTRACT.md`.
2. Copy the universal `template/` baseline into the repository.
3. Select only the profiles that apply.
4. Replace project placeholders and generate a project-specific ownership/routing map.
5. Map `.engineering/commands.json` to the repository's native setup/dev/check/test/build/smoke/package/stop/clean commands.
6. Implement applicable build identity, artifact lifecycle/build-delta and local-runtime/cleanup semantics.
7. Record adopted standard version and selected profiles in `.engineering/baseline.json`.
8. Run repository/operations/documentation/agent-context checks.
9. Add stack-specific CI/test/build/smoke gates before calling the project L0/L1 as applicable.

The `adopt-engineering-standard` Skill describes the complete workflow.

## Use with an existing project

Do not copy blindly. First audit existing architecture, docs, CI, tests, security, native commands, build/version identity, artifact/release behavior, local servers/processes/ports, cleanup and agent guidance. Preserve stronger existing practices, identify gaps/conflicts, build a small adoption DAG, then migrate incrementally.

See [`USAGE.md`](USAGE.md) for the recommended audit-first workflow and ready-to-use coding-agent prompts.

The goal is convergence on invariants and operational semantics, not identical repository layouts or build tools.

## Updating an adopted project

Projects remain self-contained. They do not depend on this repository at runtime or during ordinary coding-agent tasks.

When this baseline changes, compare the project's recorded standard version with the desired version, identify relevant semantic deltas, preserve local customizations/native tooling, and apply a focused migration. See `skills/update-engineering-standard` and [`USAGE.md`](USAGE.md).

## Documentation lifecycle

Implementation plans are disposable by default:

```text
plan -> implement -> validate -> transfer durable knowledge -> delete plan
```

Git already preserves implementation history. Keep completed plans only when they have independent audit, regulatory, release or historical value. Generated per-build deltas are artifact evidence, not active planning documents.

## Versioning

The baseline version is stored in [`VERSION`](VERSION). Changes that alter required invariants, copied Skills or machine-readable baseline semantics must be recorded in [`CHANGELOG.md`](CHANGELOG.md).

Current baseline: **0.2.0**.
