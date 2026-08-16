# Optional profiles

Profiles extend the universal engineering baseline with a small set of stack- or domain-specific expectations. They are guidance packs, not frameworks and not mandatory dependencies.

A profile should exist only when multiple repositories benefit from the same non-obvious rules or validation pattern.

Profiles also map the universal [`OPERATING-CONTRACT.md`](../OPERATING-CONTRACT.md) semantics onto native stack tooling. They must not force a universal task runner merely to make command strings identical.

Initial profile families:

- `python` — packaging, lockfiles, typing/test conventions, Python CI and native local-server/build lifecycle mapping;
- `typescript` — package-manager locking, typecheck/lint/test/build, generated-asset boundaries and native dev-server/build mapping;
- `android` — Gradle wrapper/toolchain, dependency locking, lint/static/unit/instrumented validation and APK/AAB identity/smoke mapping;
- `macos` — native lifecycle, packaging/signing, filesystem paths, built-artifact smoke validation and representative device validation;
- `local-ai` — model/resource lifecycle, memory pressure, admission/backpressure, benchmark identity and owned local-runtime cleanup.

Profiles should not duplicate universal rules from `STANDARD.md` or `OPERATING-CONTRACT.md`. A project records adopted profiles in `.engineering/baseline.json`, maps its native commands in `.engineering/commands.json`, and copies only the specific local guidance/checks it needs.
