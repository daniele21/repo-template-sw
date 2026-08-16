# macOS profile

Add only when macOS APIs, packaging or runtime behavior are material.

Minimum additions:

- explicit supported macOS/architecture targets;
- composition/lifecycle ownership for native windows, menu-bar processes, background servers, audio/video devices and helper processes;
- centralized dev-vs-bundle path resolution;
- packaging includes all runtime data/binaries and is tested from the built artifact;
- signing/notarization secrets remain external to source control;
- interruption, crash and shutdown restore temporary system/device state where applicable;
- representative Apple hardware evidence for device/audio/GPU behavior that host-only tests cannot establish.

## Operating-contract mapping

Keep Xcode/Swift/Python/native packaging tools as the implementation mechanism and map them to `.engineering/commands.json`.

Typical mapping:

- `setup` — prepare documented Xcode/Swift/Python/runtime dependencies;
- `doctor` — verify macOS version, architecture, Xcode/toolchain and required permissions/devices;
- `dev` — launch the application/runtime from source in the supported development mode;
- `check` — formatter/static/type/lint gates appropriate to the project;
- `test` — unit/integration tests;
- `build` — produce a uniquely identified `.app` or equivalent runnable artifact;
- `smoke` — launch and exercise the **built artifact**, not only the source/dev runner;
- `package` — produce `.dmg`, `.pkg`, archive or other distributable when applicable;
- `stop` — terminate project-owned helper/background/local-server processes when applicable;
- `clean` — remove only project-owned build/staging/temp output.

Every material `.app`/package build gets a unique build ID and artifact name/manifest containing source identity. Successful artifacts are immutable; failed staging output is never promoted as valid.

The default local retention is the latest two successful artifacts per platform/architecture/channel/variant lineage. Durable releases belong in GitHub Releases or an equivalent immutable release store rather than relying on local `dist/`.

Apps with loopback servers/helper processes must bind locally by default, collision-check configurable ports, clean up on success/failure/interrupt, and verify that no project-owned listener/helper remains after stop. Packaging smoke validation must include the same cleanup guarantees.
