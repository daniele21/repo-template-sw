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
- `test` — relevant unit/integration/instrumentation tests;
- `build` — produce a uniquely identified debug/internal artifact;
- `smoke` — install and launch the built artifact, exercising a minimal real path;
- `package` — produce signed/unsigned release APK/AAB as appropriate without committing signing secrets;
- `clean` — remove only project-owned generated output;
- `stop` — mark `n/a` unless the project owns a helper/local server/process that needs explicit shutdown.

Each material APK/AAB build must carry a unique build identity distinct from the product version. Put product version, build ID and source revision in the artifact name/manifest; use Android `versionCode`/`versionName` consistently with release requirements rather than incrementing the marketing version for every local build.

Keep the latest two successful local artifacts per comparable lineage by default. Device/install smoke tests and helper-server tests must clean project-owned processes, temporary data and listeners even on failure/interruption.
