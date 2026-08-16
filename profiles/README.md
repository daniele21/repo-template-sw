# Optional profiles

Profiles extend the universal engineering baseline with a small set of stack- or domain-specific expectations. They are guidance packs, not frameworks and not mandatory dependencies.

A profile should exist only when multiple repositories benefit from the same non-obvious rules or validation pattern.

Initial profile families:

- `python` — packaging, lockfiles, Ruff/typing/test conventions and Python CI;
- `typescript` — package-manager locking, typecheck/lint/test/build and generated-asset boundaries;
- `android` — Gradle wrapper/toolchain, dependency locking, lint/static/unit/instrumented validation;
- `macos` — native lifecycle, packaging/signing, filesystem paths and representative device validation;
- `local-ai` — model/resource lifecycle, memory pressure, admission/backpressure, benchmark identity and real-hardware evidence.

Profiles should not duplicate universal rules from `STANDARD.md`. A project records adopted profiles in `.engineering/baseline.json` and copies only the specific local guidance/checks it needs.
