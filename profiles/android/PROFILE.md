# Android profile

Add only when Android is a material target.

Minimum additions:

- committed Gradle wrapper and pinned compatible JDK/SDK/NDK versions where applicable;
- dependency locking/version catalogs or equivalent reproducible dependency policy;
- formatting/static analysis, unit tests, Android Lint and build gates;
- instrumentation/device validation for behavior that cannot be proven on the host;
- lifecycle-aware ownership for services, processes, threads, models, database handles and native resources;
- memory-pressure and trim/lifecycle behavior when large resident resources exist;
- native pointers/backend-specific structures never escape their owning boundary;
- emulator evidence is never presented as physical-device/production evidence.

## Operating-contract mapping

Keep Gradle/Android tooling native; map it to the common intents in `.engineering/commands.json` rather than adding an unnecessary wrapper framework.

Typical mapping:

- `setup` — verify/install documented JDK/SDK components and use the committed Gradle wrapper;
- `doctor` — verify JDK, SDK, ADB and required device/emulator capability;
- `dev` — assemble/install/launch the supported debug variant when useful;
- `check` — format/static analysis + Android Lint + fast unit gates;
- `test` — relevant unit/integration tests;
- `e2e` — critical complete app journeys using Compose UI Test, Espresso, UI Automator or the established native equivalent;
- `build` — produce a uniquely identified debug/internal artifact;
- `smoke` — install and launch the built artifact, exercising a minimal real path;
- `package` — produce signed/unsigned release APK/AAB as appropriate without committing signing secrets;
- `clean` — remove only project-owned generated output;
- `stop` — mark `n/a` unless the project owns a helper/local server/process that needs explicit shutdown.

During implementation, use the cheapest affected Gradle tasks first when the current agent has a supported Android execution environment.

Before publication, `preflight-change` selects every deterministic Android gate required by blast radius — including formatter/Spotless-style checks, static analysis/detekt-style checks, affected Kotlin/Java compilation, relevant unit/contract tests, Android Lint, R8/minification and assemble/package tasks when applicable — and classifies each for the current agent/session:

- `AGENT_LOCAL` when the agent has the required checkout/JDK/SDK/NDK/tooling;
- `REMOTE_AUTOMATED` when the gate is automatable but the current agent lacks that environment;
- `REAL_ENVIRONMENT` only when the claim genuinely depends on a representative device/hardware/manual environment.

A ChatGPT Project that can inspect/write GitHub but cannot execute Gradle must **not** ask the user to run `./gradlew`, R8, Lint, unit tests or ordinary APK builds as its normal validation loop. Those gates are `REMOTE_AUTOMATED` and must be executed through repository-owned remote preflight.

When an equivalent agent-local Gradle environment exists, a formatting, lint, compile, host-unit or deterministic R8/build failure should normally never be first discovered by GitHub Actions. If it is, treat it as local/remote parity or preflight-selection feedback and move that gate earlier.

When no equivalent agent-local environment exists, GitHub Actions or another secured remote runner is a valid execution backend. The failure should be consumed by the agent, diagnosed at the owning invariant, fixed and remotely retriggered without turning the user into a runner.

For PR-comment remote preflight, prefer trusted requesters, exact PR-head pinning, same-repository heads by default, no production/signing/deployment secrets in the code-execution job, read-only/no write credentials while PR code executes, and a separate reporting job if PR write permission is required.

## Android E2E environment fidelity

Specialize `.engineering/e2e.json` around the Android target/device dimensions that materially affect the product rather than treating every connected test as equivalent evidence.

A useful Android fidelity ladder is normally:

```text
host/JVM or fake-backed workflow
-> emulator/instrumentation workflow
-> built APK installed on emulator
-> automated representative physical device/device farm when justified
-> residual target/OEM physical-device confirmation
```

Not every project needs every rung and not every change runs every rung. Use the cheapest environment that can prove the changed critical journey, then escalate when ABI/native backend, package/install behavior, process lifecycle, permissions, hardware, OEM behavior, memory, thermals or another device-specific dimension is material.

Typical `.engineering/e2e.json` mappings:

- Android emulator/AVD: `simulated_or_emulated`;
- CI runner exercising Android host logic: `host_or_fake` unless the actual Android runtime is present;
- real supported APK on a representative physical device farm: `representative_physical`;
- the actual supported/OEM device configuration used for final acceptance: `target_environment` for the claims it truly represents.

Keep E2E small and critical: first launch, primary create/use/save flow, persistence/restart, IPC/Binder or consumer-app integration, import/export or a representative failure/recovery journey when those behaviors are product-critical. Prefer unit/integration tests for deterministic lower-level behavior.

When the product claim depends on the real APK/device surface, execute E2E on the built artifact as early as practical. A built APK on an emulator is stronger artifact evidence but remains emulator evidence for physical-device claims.

The final physical-device test should primarily validate residual device-specific gaps. Broken navigation, persistence, Binder/IPC wiring, install/launch, request/response flow or ordinary restart/recovery defects should be moved into earlier automated E2E whenever technically practical.

Device/emulator E2E must clean run-owned app data, test fixtures, helper processes and localhost listeners according to the zero-residue contract.

Physical-device/OEM/native-backend/thermal/performance/TalkBack evidence may remain explicitly pending after `AUTOMATED_PREFLIGHT_CONFIRMED` when it cannot truthfully run through ordinary automation; it still blocks stronger product-complete claims that require it.

Each material APK/AAB build must carry a unique build identity distinct from the product version. Put product version, build ID and source revision in the artifact name/manifest; use Android `versionCode`/`versionName` consistently with release requirements rather than incrementing the marketing version for every local build.

Keep the latest two successful local artifacts per comparable lineage by default. Device/install smoke tests and helper-server tests must clean project-owned processes, temporary data and listeners even on failure/interruption.
