# TypeScript profile

Add only when TypeScript/JavaScript is a material source surface.

Minimum additions:

- one declared package manager and committed lockfile;
- no dynamic dependency ranges for critical application tooling without explicit justification;
- typecheck, lint, test and production build gates appropriate to the project;
- generated/minified/hash-named assets are built from source and are not edited manually;
- API/configuration contracts have one owner rather than duplicated literals across clients;
- timers, subscriptions, workers, streams and browser/native bridges have explicit cleanup;
- user-facing persisted state has migration/reset semantics;
- bundle-size/performance budgets are added when they materially affect product quality.

## Operating-contract mapping

Use the declared package manager's native scripts/tasks behind `.engineering/commands.json`; do not add a second task runner only for naming consistency.

Typical mapping:

- `setup` — install/sync from the committed lockfile;
- `doctor` — verify Node/runtime/package-manager versions and required local tools;
- `dev` — start the development application/server in foreground mode;
- `check` — formatting/lint/typecheck plus cheap structural gates;
- `test` — unit/integration/e2e tests as appropriate;
- `build` — create a uniquely identified production/runnable build artifact;
- `smoke` — serve/run the built output and exercise a minimal real path;
- `package` — create distributable archive/desktop bundle/package when applicable;
- `stop` — stop project-owned local servers/workers when applicable;
- `clean` — remove project-owned generated bundles/staging/temp output.

Local dev/test servers bind to loopback by default unless external access is intentional, use configurable collision-aware ports, and leave no project-owned listener or worker after stop/failure/interrupt.

Material build artifacts use staging/promote, unique build identity, manifest/checksum/build delta and bounded local retention. Generated bundle assets remain reproducible from source and lockfile.
