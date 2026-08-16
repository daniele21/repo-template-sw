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
