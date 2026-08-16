# Project Operating Contract

Version: 0.2.0

This document defines the common operational semantics that adopted repositories expose to humans and coding agents. It standardizes **intent and lifecycle**, not implementation tools. Android may use Gradle, macOS may use Xcode or Python tooling, and servers may use `uv`, `python`, `pnpm` or another native mechanism, while still exposing the same operational concepts.

The governing rule is:

> Every operation must be identifiable, owned, bounded, reversible and leave no unintended residue.

## 1. Command contract

Each adopted repository declares the applicable command intents in `.engineering/commands.json`.

Canonical intents:

- `setup` — prepare the supported local development environment;
- `doctor` — verify required toolchains, SDKs, devices and local prerequisites without mutating more than necessary;
- `dev` — start the canonical local development runtime;
- `check` — run the cheapest broad static/structural validation suitable for iteration;
- `test` — run behavioral tests;
- `build` — create a runnable/build artifact with a unique build identity;
- `smoke` — exercise the built or running artifact through a minimal real path;
- `package` — create a distributable artifact when applicable;
- `stop` — stop project-owned local runtimes when applicable;
- `clean` — remove project-owned generated state without deleting unrelated/shared resources.

Repositories may mark an intent `n/a` when it is genuinely not applicable. The command vocabulary is common; the actual command remains stack-native.

`check`, `test`, `build` and `clean` should be available for normal application repositories. `dev`, `smoke`, `package` and `stop` are required only when the project has the corresponding runtime/distribution behavior.

## 2. Build identity contract

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

## 3. Artifact lineage

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

## 4. Artifact lifecycle contract

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

### CI artifacts

PR/test build artifacts are temporary evidence. Use GitHub Actions Artifacts or an equivalent CI artifact store with an explicit bounded retention period. The exact duration is project-specific.

### Releases

Durable distributable releases belong in GitHub Releases or an equivalent durable release/artifact registry. Release artifacts should be immutable after publication. Use a package/container registry when the output is genuinely a package/container consumed through that registry; do not use a package registry merely as generic binary storage.

Local `dist/` directories are convenience caches, not the durable source of released artifacts.

## 5. Build delta contract

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
- validation actually executed and its PASS/FAIL/PENDING/N/A status.

A Git diff alone is insufficient because two builds of the same commit may differ through dependencies, toolchain, configuration or packaging environment.

The build delta travels with the artifact and may also be surfaced in application diagnostics when useful.

## 6. Local runtime contract

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

## 7. Ephemeral resource / zero-residue contract

Every temporary resource created by `dev`, `test`, `build`, `smoke`, `package`, benchmark or migration tooling has an owner and deterministic cleanup path.

Examples include:

- processes and child processes;
- sockets/listeners;
- PID/lock files;
- temporary directories/files;
- test databases and local stores;
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

## 8. Run identity and isolated workspaces

Longer-lived dev/test/smoke/build runs should have a unique `run_id` when practical. Use it to namespace temporary files, logs, PID files, test databases and diagnostics.

Example:

```text
.tmp/run-20260816T202700-a81f/
```

Parallel or repeated runs must not accidentally share mutable temporary state unless that sharing is intentional and synchronized.

## 9. Test-data and environment isolation

Testing/build tooling must not pollute real user state or global development state.

Prefer:

- project-local or isolated virtual environments;
- test-specific data directories/databases;
- explicit environment overrides;
- no permanent edits to shell profiles, PATH, registry or global config unless the project genuinely requires them;
- no secrets/private data in build output, logs or cached artifacts.

## 10. Cache and log hygiene

Caches and logs are resources and require bounded lifecycle rules.

Caches define owner, namespace/version, invalidation and maximum size/retention where material. A new incompatible build must not accidentally reuse stale cache state.

Development/build/test logs must not grow indefinitely. Prefer bounded retention or run-scoped temporary logs.

## 11. Repeatability

A healthy repository should tolerate repeated cycles such as:

```text
setup -> dev -> stop -> dev -> stop -> test -> build -> smoke -> build
```

without behavior changing because earlier runs left processes, listeners, locks, temp state, stale artifacts or incompatible caches behind.

For reference-grade projects, add a lifecycle cleanliness test that snapshots relevant project-owned state, runs the operation, stops it and asserts that no unintended residue remains.

## 12. Stack mapping

Profiles refine the common contract without replacing it.

Examples:

- Android: Gradle/JDK/SDK/ADB tasks implement the common intents;
- macOS: Xcode/Swift/Python/package tooling implements the same intents;
- Python/local servers: native environment/server scripts implement them;
- TypeScript/web: package-manager scripts implement them.

The standard owns the semantics. The profile gives stack guidance. The project owns the actual commands.
