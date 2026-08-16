# repo-template-sw

Agent-native reference engineering baseline for software repositories maintained by humans and coding agents.

`repo-template-sw` is not an application framework and is not intended to become a universal build system. It is the canonical source for a small engineering standard, a reusable project bootstrap, coding-agent Skills, documentation/context governance, and deterministic repository health checks.

## What it optimizes for

- software correctness and explicit ownership;
- bounded memory/resources, concurrency and failure behavior;
- reproducible builds and validation;
- privacy/security and data lifecycle clarity;
- low repository/documentation entropy;
- low coding-agent context and token cost;
- safe parallel work through dependency-aware workstreams;
- reusable operating procedures without stuffing `AGENTS.md` with every rule.

The design principle is: **make ownership, limits, failures and costs explicit, using the simplest solution that preserves the required invariants.**

## Repository layout

- [`STANDARD.md`](STANDARD.md) — canonical L0/L1/L2 engineering standard.
- [`template/`](template/) — universal files that can be adopted into a project and then specialized locally.
- [`template/skills/`](template/skills/) — core project-local coding-agent Skills.
- [`profiles/`](profiles/) — optional stack/domain guidance; profiles add only what is genuinely specific.
- [`skills/adopt-engineering-standard/`](skills/adopt-engineering-standard/) — workflow for aligning a new/existing repository.
- [`skills/update-engineering-standard/`](skills/update-engineering-standard/) — workflow for migrating an adopted repository to a newer baseline.

## Core model

```text
AGENTS.md        -> how to orient and what is invariant
Skills           -> how to perform recurring change workflows
Active workstream-> what is being implemented now
Feature/ADR/docs -> how the system works now and why durable decisions exist
Git history      -> how the repository got here
Scripts/CI       -> deterministic enforcement
```

## Use with a new project

1. Read `STANDARD.md`.
2. Copy the universal `template/` baseline into the repository.
3. Select only the profiles that apply.
4. Replace project placeholders and generate a project-specific ownership/routing map.
5. Record adopted standard version and selected profiles in `.engineering/baseline.json`.
6. Run the repository/documentation/agent-context checks.
7. Add stack-specific CI/test/build gates before calling the project L0.

The `adopt-engineering-standard` Skill describes the complete workflow.

## Use with an existing project

Do not copy blindly. First audit the existing architecture, docs, CI, tests, security, resources and agent guidance. Preserve stronger existing practices, identify gaps/conflicts, build a small adoption DAG, then migrate incrementally.

The goal is convergence on invariants, not identical repository layouts.

## Updating an adopted project

Projects remain self-contained. They do not depend on this repository at runtime or during ordinary coding-agent tasks.

When this baseline changes, compare the project's recorded standard version with the desired version, identify relevant deltas, preserve local customizations, and apply a focused migration. See `skills/update-engineering-standard`.

## Documentation lifecycle

Implementation plans are disposable by default:

```text
plan -> implement -> validate -> transfer durable knowledge -> delete plan
```

Git already preserves implementation history. Keep completed plans only when they have independent audit, regulatory, release or historical value.

## Versioning

The baseline version is stored in [`VERSION`](VERSION). Changes that alter required invariants, copied Skills or machine-readable baseline semantics must be recorded in [`CHANGELOG.md`](CHANGELOG.md).

Current baseline: **0.1.0**.
