# Using `repo-template-sw`

This guide explains how to use `repo-template-sw` with a brand-new repository, an existing repository, and an already-adopted repository that needs to move to a newer baseline.

`repo-template-sw` is a **bootstrap, audit and migration source**. It is not a runtime dependency and should not be consulted for every ordinary coding task after a project has adopted the baseline.

The operating model is **same semantics, native implementation**: repositories share the same conceptual setup/run/check/test/E2E/build/smoke/package/cleanup lifecycle without being forced onto the same build system or E2E framework.

## Mental model

```text
repo-template-sw
      |
      +--> STANDARD.md             engineering invariants
      +--> OPERATING-CONTRACT.md   command/test/E2E/build/artifact/runtime semantics
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
  -> unit/integration/contract behavioral validation

e2e
  -> complete critical user/system workflow validation

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

An intent may be `n/a` when genuinely irrelevant. Do **not** add Make, Docker, Python wrappers, Playwright or another tool solely to make repositories look the same.

## Test vs E2E vs smoke

Keep the validation layers distinct:

```text
unit/component
   ↓
integration/contract
   ↓
end-to-end
   ↓
smoke of built/running artifact
```

- `test` owns the fast behavioral layers;
- `e2e` proves a complete critical workflow across the assembled application;
- `smoke` proves minimum viability of the built/running artifact.

Do not move all tests into E2E. Prefer the cheapest deterministic level capable of proving the claim.

A repository should use E2E when it has critical workflows whose final outcome cannot be adequately established from unit/integration tests alone. Keep the E2E set small and high-value.

Typical candidates:

```text
first launch / onboarding
primary create/use/save/reopen flow
persistence/restart
import/export
critical destructive/recovery flow
representative failure/retry path
complete AI/audio/vision pipeline
```

## E2E tooling by stack

The baseline does not mandate one framework.

```text
Browser / web
  -> prefer Playwright unless an equally strong established solution already exists

Android
  -> Compose UI Test / Espresso / UI Automator / established native equivalent

macOS native
  -> XCTest / XCUITest / established native equivalent

Python/API/local server
  -> real process + public API/protocol client

CLI
  -> real executable/subprocess + externally visible assertions

Browser-rendered desktop
  -> Playwright may be appropriate when it can reliably exercise the real runtime/package
```

When the product claim depends on packaged/distributable behavior, run E2E against the built/package artifact when technically practical.

## E2E evidence and cleanup

A failed E2E may produce useful evidence:

```text
trace
screenshot
video
logs
request/response diagnostics
```

These are **bounded CI/test artifacts**, not permanent repository content. They should carry build/run/environment identity and remain privacy-safe.

E2E inherits the zero-residue contract. A failed assertion must not leave behind:

```text
servers/listeners
browser/helper processes
browser profiles/contexts
device/emulator state owned by the run
test databases/storage
sessions/accounts that are explicitly disposable
downloads/uploads
temp workspaces
locks/PID files
unbounded traces/screenshots/videos/logs
```

---

# 2. Build identity, artifacts and cleanup

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

PR / CI / E2E evidence artifacts
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
+ validation evidence, including E2E when relevant
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

Temporary processes, browser/device sessions, locks, PID files, workspaces, test databases, logs, caches, generated secrets and other ephemeral resources need owner-aware deterministic cleanup.

---

# 3. Starting a repository from zero

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
6. decide E2E applicability and identify only critical whole-system journeys that lower-level tests cannot prove;
7. preserve stack-native E2E tooling; for new browser/web E2E prefer Playwright unless an equally strong solution already exists;
8. implement applicable build identity, artifact lifecycle/build-delta and local-runtime/zero-residue behavior;
9. configure stack-specific formatting, linting, unit/integration/E2E, build, smoke and CI gates;
10. record standard version/profiles in .engineering/baseline.json;
11. run repository, operating-contract, documentation and agent-context validation;
12. report resulting maturity truthfully.

Do not leave generic placeholders. Do not add wrappers, profiles, E2E frameworks or abstractions that the project does not need.
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

Profiles map common operating/E2E semantics to native stack behavior; they are not extra frameworks.

Copying the baseline does **not** make a repository L1/L2. Real tests, E2E where justified, cleanup behavior, artifact/runtime evidence, security, observability and performance evidence still matter.

---

# 4. Aligning an existing repository

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
- current setup/dev/check/test/E2E/build/package/clean commands;
- critical workflows and whether lower-level tests prove the complete outcome;
- existing E2E framework, suite size, flakiness and CI cadence;
- E2E failure traces/screenshots/videos/logs and retention;
- build/version/artifact naming and identity;
- artifact storage, local retention, CI retention and releases;
- whether builds generate a delta/changelog from the previous comparable build;
- localhost servers, ports, helper processes and shutdown behavior;
- temp files, locks, browser/device state, test stores, caches/logs and stale-run recovery;
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
Preserve a strong existing E2E framework; add E2E only for critical complete workflows not adequately proven below that level.
Implement only justified gaps in build identity, artifacts/build delta, runtime/cleanup and validation.
Parallelize independent work with non-overlapping write boundaries.
Run project-specific validation plus repository/operating-contract health checks.
For relevant changes execute test/E2E/build/smoke/stop at the required evidence level and verify no project-owned process/listener/browser/device/temp residue remains.
Finalize durable docs and delete the temporary adoption workstream unless it has independent audit value.
```

Typical decisions:

```text
Strong existing Playwright/Cypress/XCUITest/Espresso suite
-> KEEP, map to e2e

No critical full-workflow gap
-> e2e may be N/A or remain small

Hundreds of brittle UI tests duplicating unit behavior
-> ADAPT toward smaller critical-journey E2E

Browser project needs new E2E framework
-> prefer Playwright

API-only server
-> real process/API E2E, not Playwright

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

Server shutdown leaves port/process alive
-> CONFLICT with zero-residue invariant; fix explicitly

No BUILD_CHANGELOG per build
-> ADD generated build delta
```

Adoption is complete when the repository is self-contained, real commands are declared, stronger local mechanisms are preserved, operating-contract checks pass, and the behavior promised by the command/test/E2E/build/artifact/runtime contract actually exists.

---

# 5. Normal development after adoption

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

The agent should already know how to check/test/E2E/build/smoke/clean from the repository contract.

Use the narrowest sufficient validation while iterating. Run `e2e` only when the change or final claim crosses a critical whole-system workflow boundary; do not pay the E2E cost for every local edit.

Use `plan-workstream` only when meaningful dependencies/parallel lanes/multiple acceptance gates require coordination. At completion transfer durable truth and delete the temporary plan by default.

---

# 6. Updating an adopted repository

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

For **0.2.x -> 0.3.x**, explicitly evaluate:

- whether critical complete workflows require E2E evidence;
- canonical `e2e` command mapping or truthful `n/a`;
- existing E2E framework to KEEP/ADAPT;
- critical-journey scope and flakiness;
- built/package artifact execution where the claim requires it;
- E2E cleanup of servers, browser/device sessions, downloads and test state;
- trace/screenshot/video/log retention;
- appropriate CI cadence;
- Playwright preference only when adding new browser/web E2E tooling.

A metadata-only version bump is not a valid migration.

Projects may intentionally remain on different baseline versions while migrations are evaluated.

---

# 7. Using the standard as an audit tool

Recommended request:

```text
Review <TARGET_REPOSITORY> against daniele21/repo-template-sw.
Do not change code.

Assess architecture, ownership, complexity, resources/concurrency/failure, security/data lifecycle, observability, tests, E2E critical journeys, performance, reproducibility, repository hygiene, documentation and agent operability.

Also assess:
- canonical command coverage;
- unit/integration/E2E/smoke layering;
- E2E applicability, framework, evidence, cleanup and retention;
- build identity and reproducibility;
- artifact lineage/retention/release storage;
- manifest/checksum/build-delta quality;
- localhost/process/port ownership;
- zero-residue cleanup/repeatability.

Estimate current L0/L1/L2 maturity and rank the highest-value evidence-backed gaps.
```

---

# 8. Promoting lessons back into `repo-template-sw`

The flow is bidirectional:

```text
repo-template-sw -> projects
projects -> real-world lessons -> repo-template-sw
```

Promote a project practice only when it is genuinely cross-project, protects a meaningful invariant/recurring workflow, has proven useful in reality, does not force unnecessary architecture/dependencies, and reduces more future risk/context cost than the complexity it adds.

Prefer evidence from at least two different project contexts for non-obvious universal additions.

---

# 9. What belongs where

| Concern | Canonical owner |
| --- | --- |
| Universal engineering invariant | `repo-template-sw/STANDARD.md` |
| Universal command/test/E2E/build/artifact/runtime semantics | `repo-template-sw/OPERATING-CONTRACT.md` |
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
| E2E trace/screenshot/video/log | bounded CI/test artifact store |
| Deterministic enforceable rule | scripts / CI |

> If a machine can enforce a rule deterministically, prefer code/CI over spending coding-agent tokens explaining it repeatedly.

---

# 10. Quick reference

## New repository

```text
select profiles
 -> specialize baseline
 -> map commands.json to native tooling
 -> decide critical E2E journeys
 -> implement operating lifecycle
 -> validate
 -> start product work
```

## Existing repository

```text
audit KEEP/ADAPT/ADD/N/A/CONFLICT
 -> preserve native strengths/E2E framework
 -> map commands
 -> migrate real gaps
 -> test/E2E/build/smoke/cleanup evidence
 -> validate
```

## Ordinary development

```text
project only
 -> AGENTS.md
 -> commands.json when operational
 -> local Skill when needed
 -> cheapest sufficient validation
 -> E2E only for full-workflow claims
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
