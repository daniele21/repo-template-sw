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

During implementation, use the cheapest affected Gradle tasks first. Before publication, `preflight-change` must execute every locally reproducible deterministic Android gate required by blast radius on the exact head — including formatter/Spotless-style checks, static analysis/detekt-style checks, affected Kotlin/Java compilation, relevant unit/contract tests, Android Lint and assemble/package tasks when applicable. The exact task names remain project-owned through `.engineering/commands.json`.

A formatting, lint, compile or host-unit failure that could have been reproduced through the supported Gradle environment should normally never be first discovered by GitHub Actions. If it is, treat it as local/CI parity or preflight-selection feedback and move that gate earlier rather than accepting repeated CI patch cycles.

Keep E2E small and critical: first launch, primary create/use/save flow, persistence/restart, import/export or a representative failure/recovery journey when those behaviors are product-critical. Prefer unit/integration tests for deterministic lower-level behavior.

When the product claim depends on the real APK/device surface, execute E2E on the built artifact and on a representative physical device when emulator evidence is insufficient. Device/emulator E2E must clean run-owned app data, test fixtures, helper processes and localhost listeners according to the zero-residue contract.

Physical-device/thermal/performance/TalkBack evidence that cannot truthfully run in the local preflight environment may remain explicitly pending at `READY_FOR_CI`; it still blocks stronger product-complete claims that require it.

Each material APK/AAB build must carry a unique build identity distinct from the product version. Put product version, build ID and source revision in the artifact name/manifest; use Android `versionCode`/`versionName` consistently with release requirements rather than incrementing the marketing version for every local build.

Keep the latest two successful local artifacts per comparable lineage by default. Device/install smoke tests and helper-server tests must clean project-owned processes, temporary data and listeners even on failure/interruption.
