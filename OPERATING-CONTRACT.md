# Project Operating Contract

Version: 0.4.0

This document defines the common operational semantics that adopted repositories expose to humans and coding agents. It standardizes **intent and lifecycle**, not implementation tools. Android may use Gradle, macOS may use Xcode or Python tooling, browser applications may use Playwright or an established equivalent, and servers may use `uv`, `python`, `pnpm` or another native mechanism, while still exposing the same operational concepts.

The governing rule is:

> Every operation must be identifiable, owned, bounded, reversible and leave no unintended residue.

The delivery rule is:

> CI confirms a change that has already passed the applicable local deterministic preflight; it is not the default debugging loop.

## 1. Command contract

Each adopted repository declares the applicable command intents in `.engineering/commands.json`.

Canonical intents:

- `setup` — prepare the supported local development environment;
- `doctor` — verify required toolchains, SDKs, devices and local prerequisites without mutating more than necessary;
- `dev` — start the canonical local development runtime;
- `check` — run the cheapest broad static/structural validation suitable for iteration;
- `test` — run behavioral tests, normally unit/integration scoped according to the project;
- `e2e` — exercise a complete critical user/system workflow across its real application boundaries when lower-level tests cannot establish the full outcome;
- `build` — create a runnable/build artifact with a unique build identity;
- `smoke` — exercise the built or running artifact through a minimal real path;
- `package` — create a distributable artifact when applicable;
- `stop` — stop project-owned local runtimes when applicable;
- `clean` — remove project-owned generated state without deleting unrelated/shared resources.

Repositories may mark an intent `n/a` when it is genuinely not applicable. The command vocabulary is common; the actual command remains stack-native.

`check`, `test`, `build` and `clean` should be available for normal application repositories. `e2e` is recommended when the product has a meaningful whole-system/user journey that is not adequately proven by lower-level tests. `dev`, `smoke`, `package` and `stop` are required only when the project has the corresponding runtime/distribution behavior.

The repository also declares a machine-readable `publication_gate` in `.engineering/commands.json`. This gate does not introduce a universal wrapper command: the coding agent selects the existing canonical intents required by the change's blast radius and runs those native project commands before publication.

## 2. Pre-publication readiness contract

Before pushing a change, opening/updating a pull request, or intentionally triggering CI for readiness confirmation, the exact current head must pass a local preflight whenever the required evidence is reproducible in the supported local environment.

The governing principle is:

```text
understand
-> resolve ownership/invariants
-> resolve material ambiguity
-> implement
-> focused validation
-> diagnose root cause of failures
-> verify target-base freshness
-> review complete diff
-> run final local deterministic gates
-> READY_FOR_CI
-> independent CI confirmation
```

### Material ambiguity gate

A coding agent must first inspect canonical repository evidence — owner, architecture/feature docs, ADRs, direct consumers, fakes and tests. Ask the user only when a material decision remains unresolved.

An ambiguity is material when two reasonable interpretations would produce meaningfully different:

- product behavior or acceptance criteria;
- public/API/protocol contracts;
- persisted data, migration or retention semantics;
- security/trust/privacy boundaries;
- failure, concurrency, resource or lifecycle semantics;
- backward compatibility;
- meaningful user task/UX behavior.

Implementation-local naming, formatting and equivalent choices that preserve observable semantics do not require user clarification.

If the user cannot be reached, unresolved material semantics remain blocked/conditional; they must not be silently guessed merely to continue coding.

### Failure diagnosis gate

A failing gate is evidence to diagnose, not a request to patch the nearest symptom. Before changing production behavior in response to a failure, classify it as one of:

- regression caused by the current change;
- failure already present on the intended target base;
- environment/toolchain/dependency mismatch;
- flaky/non-deterministic test;
- stale branch/base integration effect;
- incorrect requirement/design/contract assumption.

Identify the violated invariant and its owner, then fix that owner and add or strengthen regression evidence at the lowest useful test level. Do not delete, suppress, relax or rewrite a legitimate gate merely to obtain green status without explicitly changing the owning contract.

If the same gate fails again after an attempted fix, re-evaluate the cause/assumption before applying another patch. Repeated symptom patching without a new falsifiable hypothesis is not acceptable engineering progress.

### Target-base freshness gate

Preflight evidence is relative to both the exact feature head and its intended integration base.

Before `READY_FOR_CI`:

- refresh knowledge of the intended target branch;
- verify that the feature work is based on, reconciled with, or otherwise proven merge-compatible with the current target according to the repository's branch model;
- treat stacked branches as conditional until parent dependencies are integrated and the branch is replayed/revalidated where necessary;
- rerun gates invalidated by a changed head, dependency or base relationship.

Prior green evidence from an older head/base is historical evidence, not current readiness.

### Complete-diff gate

Review the complete change against the intended base, not only files edited most recently. Check for accidental generated files, debug/logging residue, unrelated edits, duplicated ownership/policy, weakened tests, stale documentation, hidden compatibility changes and unbounded resource/lifecycle consequences.

### Local deterministic gate

Use `validate-change` to determine the required validation level, then execute every applicable locally reproducible deterministic gate before publication. Typical examples include:

- formatter/format check;
- lint/static analysis/typecheck;
- affected compilation;
- focused unit/component tests;
- direct-consumer/contract/integration tests;
- repository `check`/`test` as required by blast radius;
- build/package/smoke where the claim depends on them.

Do not run expensive unrelated suites for every edit, but do not publish after only a focused unit test when the change crosses shared/build/runtime boundaries.

A project may have evidence that cannot truthfully run locally, such as physical-device, specialized hardware, external-service or representative-user validation. Such evidence must be declared `PENDING/CI_ONLY/DEVICE` rather than being silently treated as passed. It can remain pending for `READY_FOR_CI` only when it is genuinely not locally reproducible; it still blocks any stronger completion claim that requires it.

### Readiness result

Report preflight evidence as `PASS`, `FAIL`, `PENDING` or `N/A` and distinguish:

- `READY_FOR_CI` — no unresolved material ambiguity; target/base and full diff reviewed; every required locally reproducible deterministic gate passed on the exact current head; CI-only/real-environment evidence explicitly declared;
- `NOT_READY_FOR_CI` — any required local deterministic gate failed/pending, material ambiguity remains, or exact-head/base readiness is unknown;
- `CI_CONFIRMED` — remote independent checks passed for that exact head/base relationship;
- stronger product/release readiness — only after any required device/hardware/external/manual evidence also passes.

Publishing a known-red draft may be done only when explicitly requested for collaboration/investigation, and the known-red state must be stated clearly rather than represented as readiness.

### Local/CI parity

Deterministic CI validation should invoke the same project-owned canonical commands/scripts used locally where practical. Do not maintain a hidden second implementation of formatting/test/build policy only in workflow YAML.

When CI finds a deterministic repository failure that local preflight should have found, treat it as a parity/process defect. Fix command routing, environment setup, validation selection or agent procedure so the same class of failure moves earlier.

## 3. Validation boundary: test vs E2E vs smoke

These intents are intentionally distinct:

```text
unit/component
    ↓
integration/contract
    ↓
end-to-end
    ↓
smoke of built/running artifact
```

- `test` remains the primary fast behavioral validation surface and may include unit/integration/contract suites;
- `e2e` proves a complete user/system outcome that crosses multiple real boundaries;
- `smoke` proves minimum viability of the built/running artifact or runtime, not complete business behavior.

Do not move all testing into E2E. E2E suites are slower, more failure-prone and more expensive to diagnose. Cover invariants as low in the test pyramid as practical, and reserve E2E for critical journeys whose correctness depends on the assembled system.

Typical critical journeys include, when applicable:

- first launch/onboarding and core product entry;
- create/use/save/reopen or equivalent primary workflow;
- import/export or persistence/restart paths;
- a critical destructive/recovery flow;
- one representative failure/retry/recovery path;
- authentication/payment/submission flows when they are part of the product's trust boundary;
- complete local-AI/audio/vision workflows when correctness depends on multiple runtime stages.

E2E is evidence, not coverage theater. Prefer a small deterministic set of high-value journeys over hundreds of brittle UI scripts.

## 4. E2E implementation contract

Use the smallest reliable stack-native tool already appropriate to the project. The universal baseline does **not** mandate Playwright.

Examples:

- browser/web: prefer Playwright unless the project already has an equally strong established E2E solution;
- Android: Compose UI Test, Espresso, UI Automator or the established native equivalent;
- macOS/iOS native: XCTest/XCUITest or the established native equivalent;
- Python/API/local server: start the real process and exercise the public API/protocol with the project's native test client;
- CLI: launch the real executable/subprocess and verify end-to-end input/output/state;
- desktop applications with browser-rendered UI: Playwright may be appropriate when it can exercise the real packaged/runtime surface reliably.

When the claim is about a distributable artifact, E2E should run against the built/package artifact when technically practical rather than only a source/dev runner.

E2E failure evidence should carry enough identity to diagnose the run:

- build/source/run identity;
- environment/platform/device/browser identity when material;
- logs/error classification;
- trace, screenshot, video or request/response evidence when useful and privacy-safe.

Failure evidence is temporary CI/test evidence, not permanent repository content. Store it through the artifact lifecycle with bounded retention.

## 5. E2E zero-residue contract

E2E runs inherit the same cleanup guarantees as every other project operation.

An E2E run may own:

- application/server/browser/device processes;
- localhost listeners;
- browser profiles/contexts;
- emulator/simulator/device state created for the run;
- test accounts/sessions when locally owned or explicitly disposable;
- temporary databases/storage/preferences;
- downloads/uploads/test fixtures;
- screenshots, traces and videos;
- temp workspaces, PID/lock files and logs.

The owner must clean or explicitly retain these resources according to policy on success, failure, timeout, cancellation and interrupt. A failed E2E test is not allowed to leave a localhost server, browser worker or helper process alive merely because the assertion failed.

## 6. Build identity contract

Every material build has a unique identity even when the same source revision is rebuilt.

Build identity should distinguish:

- product name;
- product/release version;
- unique build ID;
- source revision;
- dirty/modified source state when applicable;
- platform/architecture;
- build channel/variant.

A recommended artifact name is:

```text
<Product>-<ProductVersion>-<BuildId>-<SourceRevision>[-dirty].<ext>
```

A new build must not silently overwrite a previous build. Product version and build identity are separate concepts: rebuilding version `1.4.0` creates a new build ID rather than pretending the product version changed.

The build identity must be discoverable from the artifact itself or its adjacent manifest, and preferably from an application's About/Diagnostics surface when practical.

## 7. Artifact lineage

Artifacts are compared and retained within a comparable lineage, normally:

```text
project / platform / architecture / channel / variant
```

Examples:

```text
closedroom/macos/arm64/dev/default
android-harness/android/arm64/debug/default
local-llm-server/macos/arm64/dev/server
```

The "previous build" means the previous **successful comparable build in the same lineage**, not simply the newest file in a directory.

## 8. Artifact lifecycle contract

Build artifacts are immutable outputs. Once a build ID is assigned and an artifact is promoted as successful, modifying that artifact in place is forbidden. A changed output receives a new build ID.

Each successful artifact should have:

- artifact file(s);
- `build-manifest.json` or equivalent machine-readable manifest;
- `BUILD_CHANGELOG.md` or equivalent generated build delta;
- SHA-256 checksums for distributable binaries/packages;
- enough source/toolchain/configuration identity to reproduce or diagnose the build.

Builds use a staging/promote flow:

```text
staging -> build -> validate -> promote -> manifest/delta/checksum -> retention -> verify clean
```

A failed/partial build must never be left in a location where it can be mistaken for a valid artifact.

### Local retention

The default local policy is to keep the latest **two successful builds per artifact lineage**. Projects may retain more only with a concrete workflow need. Retention is bounded and automatic; old local artifacts must not accumulate indefinitely.

### CI and E2E evidence artifacts

PR/test/E2E artifacts are temporary evidence. Use GitHub Actions Artifacts or an equivalent CI artifact store with an explicit bounded retention period. Screenshots, traces, videos, logs and test downloads should not accumulate in Git history or indefinitely on developer machines.

### Releases

Durable distributable releases belong in GitHub Releases or an equivalent durable release/artifact registry. Release artifacts should be immutable after publication. Use a package/container registry when the output is genuinely a package/container consumed through that registry; do not use a package registry merely as generic binary storage.

Local `dist/` directories are convenience caches, not the durable source of released artifacts.

## 9. Build delta contract

Every successful build generates a build delta against the previous successful comparable build in its lineage.

The build delta is distinct from the product-level `CHANGELOG.md`:

- `CHANGELOG.md` describes durable product/release history;
- `BUILD_CHANGELOG.md` describes the exact delta from build N-1 to build N.

The generated build delta should report, when applicable:

- previous/current build ID and source revision;
- source commits/PRs or equivalent source changes;
- dependency/lockfile changes;
- toolchain/SDK/runtime changes;
- build/configuration/feature-flag changes;
- migrations or compatibility implications;
- artifact size/hash changes;
- validation actually executed, including relevant E2E evidence, with PASS/FAIL/PENDING/N/A status.

A Git diff alone is insufficient because two builds of the same commit may differ through dependencies, toolchain, configuration or packaging environment.

The build delta travels with the artifact and may also be surfaced in application diagnostics when useful.

## 10. Local runtime contract

Projects that open local servers, sockets, helper processes or listeners must declare and own them explicitly.

Defaults:

- bind to loopback (`127.0.0.1`/`::1`) unless external exposure is intentional;
- ports are configurable and collision-checked before start;
- development runtimes run in the foreground by default;
- readiness/health is explicit when the runtime accepts requests;
- shutdown is graceful and bounded;
- child/helper processes are owned by the launching operation;
- `stop`, timeout, cancellation, failure and interrupt all execute cleanup;
- after shutdown, verify that no **project-owned listener** remains open.

Normal kernel TCP states such as `TIME_WAIT` are not considered an open application listener. The invariant is that no project-owned process continues listening after the owning operation is complete.

A strong server smoke lifecycle is:

```text
allocate run id
-> select/check port
-> start
-> wait readiness
-> minimal request
-> shutdown
-> wait process exit
-> verify children gone
-> verify listener gone
-> verify temporary resources clean
```

A strong server E2E lifecycle extends that minimal request into one complete critical workflow, then performs the same cleanup verification.

## 11. Ephemeral resource / zero-residue contract

Every temporary resource created by `dev`, `test`, `e2e`, `build`, `smoke`, `package`, benchmark or migration tooling has an owner and deterministic cleanup path.

Examples include:

- processes and child processes;
- sockets/listeners;
- PID/lock files;
- temporary directories/files;
- test databases and local stores;
- browser/device test state;
- downloads, screenshots, traces and videos;
- mounts/device state;
- generated secrets/certificates;
- logs;
- caches;
- reservations and resource leases.

Cleanup must run on:

- success;
- failure;
- timeout;
- cancellation;
- user interrupt;
- partial initialization.

Use `finally`, traps, scoped resource managers or equivalent mechanisms rather than relying on cleanup being the final happy-path command.

A new operation should detect stale resources from an earlier crash and recover them safely using ownership/identity rather than deleting blindly.

`clean` may remove only resources the project can prove it owns.

## 12. Run identity and isolated workspaces

Longer-lived dev/test/e2e/smoke/build runs should have a unique `run_id` when practical. Use it to namespace temporary files, logs, PID files, test databases and diagnostics.

Example:

```text
.tmp/run-20260816T202700-a81f/
```

Parallel or repeated runs must not accidentally share mutable temporary state unless that sharing is intentional and synchronized.

## 13. Test-data and environment isolation

Testing/build tooling must not pollute real user state or global development state.

Prefer:

- project-local or isolated virtual environments;
- test-specific data directories/databases;
- explicit environment overrides;
- no permanent edits to shell profiles, PATH, registry or global config unless the project genuinely requires them;
- no secrets/private data in build output, logs, screenshots, traces or cached artifacts.

E2E accounts/data should be disposable or namespaced when possible. Never run destructive E2E cleanup against production/user data without an explicit protected test boundary.

## 14. Cache and log hygiene

Caches and logs are resources and require bounded lifecycle rules.

Caches define owner, namespace/version, invalidation and maximum size/retention where material. A new incompatible build must not accidentally reuse stale cache state.

Development/build/test/E2E logs must not grow indefinitely. Prefer bounded retention or run-scoped temporary logs.

## 15. Repeatability

A healthy repository should tolerate repeated cycles such as:

```text
setup -> dev -> stop -> test -> e2e -> build -> smoke -> e2e -> clean
```

without behavior changing because earlier runs left processes, listeners, locks, temp state, stale artifacts, browser/device state or incompatible caches behind.

For reference-grade projects, add lifecycle cleanliness tests for important dev/test/e2e/build/smoke paths that snapshot relevant project-owned state, run the operation, stop it and assert that no unintended residue remains.

## 16. Stack mapping

Profiles refine the common contract without replacing it.

Examples:

- Android: Gradle/JDK/SDK/ADB plus native UI/device-test tooling implement the common intents;
- macOS: Xcode/Swift/Python/package tooling plus native UI-test tooling implement the same intents;
- Python/local servers: native environment/server/client scripts implement them;
- TypeScript/web: package-manager scripts implement them, with Playwright preferred for browser E2E unless an equally strong established solution already exists.

The standard owns the semantics. The profile gives stack guidance. The project owns the actual commands and E2E framework choice.
