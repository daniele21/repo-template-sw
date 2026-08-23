# Optional profiles

Profiles extend the universal engineering baseline with a small set of stack-, domain- or product-surface-specific expectations. They are guidance packs, not frameworks and not mandatory dependencies.

A profile should exist only when multiple repositories benefit from the same non-obvious rules or validation pattern.

Profiles map universal contracts onto native stack/product behavior. They must not force a universal task runner, E2E framework, design tool or visual style merely to make repositories look identical.

Current profile families:

- `python` — packaging, lockfiles, typing/test conventions, process/API E2E, Python CI and native local-server/build lifecycle mapping;
- `typescript` — package-manager locking, typecheck/lint/test/build, generated-asset boundaries, native dev-server/build mapping and Playwright-preferred browser E2E when a new framework is needed;
- `android` — Gradle wrapper/toolchain, dependency locking, lint/static/unit/instrumented validation, native UI E2E and APK/AAB identity/smoke mapping;
- `macos` — native lifecycle, packaging/signing, filesystem paths, XCTest/XCUITest-style E2E, built-artifact smoke validation and representative device validation;
- `local-ai` — model/resource lifecycle, memory pressure, admission/backpressure, complete pipeline E2E, benchmark identity and owned local-runtime cleanup;
- `product-ui` — ordered user-outcome-first UX reasoning, progressive disclosure, information/action hierarchy, cognitive-load control, complete UI states, accessibility, adaptive layouts, semantic motion/graphics, brand/design-system ownership, critical journeys and UX regression evidence for products with a material interface.

Stack profiles primarily refine [`OPERATING-CONTRACT.md`](../OPERATING-CONTRACT.md). `product-ui` primarily refines [`PRODUCT-EXPERIENCE-CONTRACT.md`](../PRODUCT-EXPERIENCE-CONTRACT.md) and can be combined with any UI-bearing stack profile.

Examples:

```text
Android local-AI app
-> android + local-ai + product-ui

macOS desktop app
-> macos + product-ui

TypeScript web app
-> typescript + product-ui

headless Python API
-> python
```

A project records adopted profiles in `.engineering/baseline.json`, maps native commands in `.engineering/commands.json`, and copies/specializes only the local guidance and contracts it actually needs.
