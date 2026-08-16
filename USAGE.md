# Using `repo-template-sw`

This guide explains how to use `repo-template-sw` with a brand-new repository, an existing repository, and an already-adopted repository that needs to move to a newer baseline.

`repo-template-sw` is a **bootstrap, audit and migration source**. It is not a runtime dependency and should not be consulted for every ordinary coding task after a project has adopted the baseline.

The current baseline also defines a common project operating model. The rule is **same semantics, native implementation**: repositories share the same conceptual setup/run/check/test/build/smoke/package/cleanup lifecycle without being forced onto the same build system.

## Mental model

```text
repo-template-sw
      |
      +--> STANDARD.md             engineering invariants
      +--> OPERATING-CONTRACT.md   command/build/artifact/runtime semantics
      +--> template/               adoptable baseline
      +--> profiles/               stack-specific mapping
      |
      +--> new repository      -> bootstrap and specialize
      +--> existing repository -> audit, preserve, align
      +--> adopted repository  -> explicit baseline migration

After adoption:

project repository
      |
      +--> AGENTS.md                    routing and durable invariants
      +--> .engineering/commands.json  canonical project operations
      +--> local Skills                 recurring development workflows
      +--> active plan                  only current coordinated work
      +--> durable docs                 how the system works now
      +--> scripts / CI                 deterministic enforcement
```

The goal is not to make repositories look identical. The goal is to make them converge on common engineering and operational invariants while remaining self-contained and adapted to their stack and product.

---

# 1. The common operating model

Every adopted application/runtime repository declares the applicable intents in `.engineering/commands.json`:

```text
setup
  -> prepare the supported environment

doctor
  -> diagnose toolchain/device/runtime readiness

dev
  -> start the canonical development runtime

check
  -> broad cheap validation

test
  -> behavioral validation

build
  -> create a uniquely identified runnable/build artifact

smoke
  -> exercise the built/running artifact through a minimal real path

package
  -> create a distributable artifact when applicable

stop
  -> stop project-owned runtimes/processes when applicable

clean
  -> remove only project-owned generated state
```

An intent may be `n/a` when genuinely irrelevant. Do **not** add Make, Docker, Python wrappers or another task runner solely to make command strings look the same.

Examples:

```text
Android
  check   -> Gradle lint/static/unit gates
  build   -> Gradle debug/internal build
  smoke   -> install + launch built APK on target

macOS
  build   -> Xcode/Swift/Python-native .app build
  smoke   -> launch the built .app, not only source/dev mode
  package -> DMG/PKG/archive when relevant

Python localhost server
  dev     -> native server command in foreground
  smoke   -> start -> ready -> request -> stop -> verify clean

TypeScript
  check   -> package-manager lint/typecheck
  build   -> production build
  smoke   -> serve/run built output and exercise minimal path
```

## Build identity

A material build always gets a new build identity even if the product version and source revision are unchanged.

Recommended artifact shape:

```text
<Product>-<ProductVersion>-<BuildId>-<SourceRevision>[-dirty].<ext>
```

Example:

```text
ClosedRoom-1.3.0-b0042-a81fc92.dmg
```

Product version and build ID are separate. A rebuild of product version `1.3.0` becomes a new build, not a fake product-version bump.

## Artifact lifecycle

Successful artifacts are immutable. Use:

```text
staging
 -> build
 -> validate
 -> promote successful artifact
 -> build-manifest.json
 -> BUILD_CHANGELOG.md
 -> SHA-256
 -> retention
 -> verify clean
```

A failed/partial build stays in staging or is cleaned; it must not look like a valid artifact.

Default storage policy:

```text
local successful artifacts
  -> latest 2 per comparable lineage

PR/CI artifacts
  -> GitHub Actions Artifacts or equivalent
  -> explicit bounded retention

release artifacts
  -> GitHub Releases or equivalent durable immutable release store

packages/containers
  -> package/container registry only when genuinely consumed that way
```

Local `dist/` is convenience storage, not the durable release registry.

## Build changelog / build delta

Every successful material build generates a build delta against the previous successful comparable build in the same lineage.

`BUILD_CHANGELOG.md` is not the product `CHANGELOG.md`.

It should cover applicable changes in:

```text
source
+ dependencies
+ toolchain/SDK/runtime
+ configuration/build flags
+ compatibility/migrations
+ artifact size/hash
+ validation evidence
```

A Git log alone is not sufficient because two builds of the same commit may differ through toolchain, dependencies or configuration.

## Local runtime and zero residue

For projects that open localhost servers, helpers, sockets or listeners:

```text
bind default    -> loopback
port            -> configurable + collision checked
startup         -> foreground by default
readiness       -> explicit
shutdown        -> graceful + bounded
cleanup         -> success/failure/timeout/cancel/interrupt
post-condition  -> no project-owned listener/process remains
```

TCP `TIME_WAIT` is not an open application listener. The invariant is that no project-owned process is still listening after the owning operation finishes.

Temporary processes, locks, PID files, workspaces, test databases, logs, caches, generated secrets and other ephemeral resources need owner-aware deterministic cleanup.

---

# 2. Starting a repository from zero

Use this path when the product repository is new or contains no meaningful engineering structure yet.

## Recommended request to a coding agent

```text
Bootstrap <TARGET_REPOSITORY> using daniele21/repo-template-sw at the current stable baseline.

Use adopt-engineering-standard.

Before implementing product features:
1. identify product/runtime, languages, platforms, persistence, network/security and build/distribution boundaries;
2. select only the applicable profiles;
3. adopt and specialize the universal baseline;
4. create a project-specific AGENTS.md with real ownership/routing;
5. map .engineering/commands.json to the repository's native tooling;
6. implement applicable build identity, artifact lifecycle/build-delta and local-runtime/zero-residue behavior;
7. configure stack-specific formatting, linting, tests, build, smoke and CI gates;
8. record standard version/profiles in .engineering/baseline.json;
9. run repository, operating-contract, documentation and agent-context validation;
10. report resulting maturity truthfully.

Do not leave generic placeholders. Do not add wrappers, profiles or abstractions that the project does not need.
```

## What the agent should take from the template

The universal baseline under `template/` provides:

- `AGENTS.md`;
- `CONTRIBUTING.md`;
- `SECURITY.md`;
- `.engineering/baseline.json`;
- `.engineering/documentation-policy.json`;
- `.engineering/commands.json`;
- architecture/current-state/feature/ADR/workstream structure;
- project-local core Skills;
- repository/operations/documentation/agent-context validators;
- baseline PR and repository-health workflow.

The agent must **specialize** these files. Copying placeholders or leaving fake commands is not completed adoption.

## Select profiles deliberately

Examples:

```text
Python local inference service
-> core + python + local-ai

Android application
-> core + android

Android application running local AI
-> core + android + local-ai

macOS Python application with local models
-> core + python + macos + local-ai

TypeScript web application
-> core + typescript
```

Profiles map the common operating semantics to native stack behavior; they are not extra frameworks.

## Expected responsibilities

A new project should end bootstrap with responsibilities similar to:

```text
project/
├── AGENTS.md
├── README.md
├── CONTRIBUTING.md
├── SECURITY.md
├── .engineering/
│   ├── baseline.json
│   ├── documentation-policy.json
│   └── commands.json
├── docs/
│   ├── architecture.md
│   ├── current-state.md
│   ├── adr/
│   ├── features/
│   └── workstreams/
├── skills/
├── scripts/
│   ├── verify_repository.py
│   ├── verify_operations.py
│   ├── verify_docs.py
│   └── verify_agent_context.py
└── project-specific source, tests and CI
```

The exact folder layout may differ when a stronger project-specific structure or stack constraint justifies it.

Copying the baseline does **not** make a repository L1/L2. Real tests, cleanup behavior, artifact/runtime evidence, security, observability and performance evidence still matter.

---

# 3. Aligning an existing repository

Use this path when the project already has code, scripts, build tooling, tests, documentation, CI or agent instructions.

**Do not copy `template/` over the repository. Audit first.**

## Recommended first request: audit only

```text
Audit <TARGET_REPOSITORY> against the current stable baseline of daniele21/repo-template-sw.

Use adopt-engineering-standard, but do not modify the repository yet.

For every relevant concern classify the project as KEEP / ADAPT / ADD / N/A / CONFLICT.

Inspect specifically:
- architecture/ownership;
- AGENTS.md/Skills/docs;
- current setup/dev/check/test/build/package/clean commands;
- build/version/artifact naming and identity;
- artifact storage, local retention, CI retention and releases;
- whether builds generate a delta/changelog from the previous comparable build;
- localhost servers, ports, helper processes and shutdown behavior;
- temp files, locks, test stores, caches/logs and stale-run recovery;
- CI/testing/reproducibility;
- security/data lifecycle;
- resource/memory/concurrency/failure behavior.

Return:
- estimated L0/L1/L2 maturity;
- KEEP / ADAPT / ADD / N/A / CONFLICT matrix;
- what should explicitly NOT be changed;
- highest-value gaps;
- a small dependency-aware adoption DAG;
- tasks that can safely run in parallel.
```

The audit prevents standardization from destroying better native workflows.

## Recommended second request: implement

```text
Implement the approved repo-template-sw adoption plan for <TARGET_REPOSITORY>.

Preserve KEEP items and native tooling.
Map existing commands semantically into .engineering/commands.json instead of replacing them unnecessarily.
Implement only justified gaps in build identity, artifacts/build delta, runtime/cleanup and validation.
Parallelize independent work with non-overlapping write boundaries.
Run project-specific validation plus repository/operating-contract health checks.
For runtime/build changes execute an applicable build/smoke/stop cycle and verify no project-owned process/listener/temp residue remains.
Finalize durable docs and delete the temporary adoption workstream unless it has independent audit value.
```

Typical decisions:

```text
Strong existing Gradle/Xcode build pipeline
-> KEEP + expose through command contract

Existing native script named run_local.sh
-> KEEP/ADAPT as the dev implementation

No unique build identity
-> ADD

Builds overwrite app.dmg/app.apk
-> ADD immutable identity/promotion

Hundreds of local builds retained
-> ADAPT bounded retention

CI artifact storage already bounded
-> KEEP

Release binaries only local
-> ADD durable release store

Server shutdown leaves port/process alive
-> CONFLICT with zero-residue invariant; fix explicitly

Existing artifact manifest/checksum stronger than baseline
-> KEEP

No BUILD_CHANGELOG per build
-> ADD generated build delta
```

Adoption is complete when the repository is self-contained, real commands are declared, stronger local mechanisms are preserved, operating-contract checks pass, and the behavior promised by the command/build/artifact/runtime contract actually exists.

---

# 4. Normal development after adoption

After adoption, **do not use `repo-template-sw` for every feature or bug fix**.

Normal work uses only the target repository:

```text
user request
    |
    v
AGENTS.md
    |
    +--> closest scoped guide if needed
    +--> .engineering/commands.json when operational commands matter
    +--> local Skill when workflow requires it
    +--> focused code/tests/docs
```

A normal request can stay small:

```text
Implement memory-aware eviction.
Parallelize independent work where safe.
```

The agent should already know how to check/test/build/smoke/clean from the repository contract.

Use `plan-workstream` only when meaningful dependencies/parallel lanes/multiple acceptance gates require coordination. At completion transfer durable truth and delete the temporary plan by default.

---

# 5. Updating an adopted repository

Do not automatically sync template commits into all projects.

Recommended request:

```text
Migrate <TARGET_REPOSITORY> from its recorded repo-template-sw baseline to <TARGET_VERSION>.

Use update-engineering-standard.
Read baseline.json, commands.json and local customizations.
Compare VERSION/CHANGELOG plus relevant standard/contract deltas.
Classify every delta APPLY / MERGE / N/A / DEFER / CONFLICT.
Preserve native tooling and stronger local behavior.
Apply semantic changes, validate them, then update baseline/command-contract metadata.
```

For **0.1.x -> 0.2.0**, explicitly evaluate:

- common command routing;
- unique build identity;
- artifact lineage/immutability/staging-promotion;
- manifests/checksums;
- local latest-two retention default;
- CI vs release storage;
- generated build delta;
- local server/process/port cleanup;
- temp/lock/test-store/log/cache cleanup;
- `verify_operations.py` in CI.

A metadata-only bump is not a valid migration.

Projects may intentionally remain on different baseline versions while migrations are evaluated.

---

# 6. Using the standard as an audit tool

Recommended request:

```text
Review <TARGET_REPOSITORY> against daniele21/repo-template-sw.
Do not change code.

Assess architecture, ownership, complexity, resources/concurrency/failure, security/data lifecycle, observability, tests, performance, reproducibility, repository hygiene, documentation and agent operability.

Also assess:
- canonical command coverage;
- build identity and reproducibility;
- artifact lineage/retention/release storage;
- manifest/checksum/build-delta quality;
- localhost/process/port ownership;
- zero-residue cleanup/repeatability.

Estimate current L0/L1/L2 maturity and rank the highest-value evidence-backed gaps.
```

---

# 7. Promoting lessons back into `repo-template-sw`

The flow is bidirectional:

```text
repo-template-sw -> projects
projects -> real-world lessons -> repo-template-sw
```

Promote a project practice only when it is genuinely cross-project, protects a meaningful invariant/recurring workflow, has proven useful in reality, does not force unnecessary architecture/dependencies, and reduces more future risk/context cost than the complexity it adds.

Prefer evidence from at least two different project contexts for non-obvious universal additions.

---

# 8. What belongs where

| Concern | Canonical owner |
| --- | --- |
| Universal engineering invariant | `repo-template-sw/STANDARD.md` |
| Universal command/build/artifact/runtime semantics | `repo-template-sw/OPERATING-CONTRACT.md` |
| Universal adoption/update procedure | `repo-template-sw/skills/` |
| Optional stack/domain mapping | `repo-template-sw/profiles/` |
| Project command implementation/policy mapping | project `.engineering/commands.json` |
| Project routing/invariants | project `AGENTS.md` |
| Recurring project development procedure | project `skills/` |
| Current coordinated implementation | project active workstream |
| Current system behavior | project feature/architecture docs |
| Durable architecture decision | project ADR |
| Implementation history | Git |
| Per-build exact delta | artifact `BUILD_CHANGELOG.md` |
| Deterministic enforceable rule | scripts / CI |

> If a machine can enforce a rule deterministically, prefer code/CI over spending coding-agent tokens explaining it repeatedly.

---

# 9. Quick reference

## New repository

```text
select profiles
 -> specialize baseline
 -> map commands.json to native tooling
 -> implement operating lifecycle
 -> validate
 -> start product work
```

## Existing repository

```text
audit KEEP/ADAPT/ADD/N/A/CONFLICT
 -> preserve native strengths
 -> map commands
 -> migrate real gaps
 -> build/smoke/cleanup evidence
 -> validate
```

## Ordinary development

```text
project only
 -> AGENTS.md
 -> commands.json when operational
 -> local Skill when needed
 -> code/tests/build/smoke as applicable
```

## Baseline upgrade

```text
baseline.json + commands.json
 -> VERSION/CHANGELOG/contract delta
 -> APPLY/MERGE/N/A/DEFER/CONFLICT
 -> focused migration
 -> validate
 -> metadata update
```

The intended outcome is simple: **use `repo-template-sw` to establish and evolve the engineering system, then let each project operate independently with the same clear semantics and its own native implementation.**
