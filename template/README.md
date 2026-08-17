# <PROJECT_NAME>

<REPLACE_WITH_A_SHORT_PRODUCT_OR_LIBRARY_DESCRIPTION>

## Why this exists

<REPLACE_WITH_THE_PRIMARY_USER_OR_SYSTEM_OUTCOME>

## Architecture

See [`docs/architecture.md`](docs/architecture.md) for current boundaries and ownership. Keep this README focused on purpose, setup and public usage rather than implementation history.

## Project commands

The canonical setup/dev/validation/build/package/cleanup mapping lives in [`.engineering/commands.json`](.engineering/commands.json). It exposes `setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop` and `clean` while keeping this project's native tooling underneath.

Do not add a second undocumented command path for the same intent.

## Product experience

If `.engineering/baseline.json` includes `product-ui`, the canonical project experience/brand contracts live in [`design/ux-contract.json`](design/ux-contract.json) and [`design/brand-kit.json`](design/brand-kit.json).

They define or point to information hierarchy, progressive disclosure, critical states/journeys, accessibility, adaptive layout, brand/design tokens, component ownership and key reference views. They do not require one design tool or visual style.

If `product-ui` is not adopted, this section/design baseline is not applicable and may be removed during specialization.

## Setup

Use the command declared as `setup` in `.engineering/commands.json`, then `doctor` when environment diagnostics are needed.

## Run

Use the declared `dev` command when applicable. Local servers/processes must follow the repository's runtime/cleanup contract and leave no project-owned listeners/processes after stop.

## Build and artifacts

Use the declared `build`/`package` commands. Material builds use unique build identity, immutable successful artifacts, manifests/checksums, bounded local retention and a generated build delta against the previous successful comparable build.

## Validate

Start with repository health checks:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_operations.py
python3 scripts/verify_product_experience.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

`verify_product_experience.py` passes as `N/A` unless `product-ui` is adopted.

Then use `check`/`test` while iterating. Use `e2e` when a critical complete workflow must be proven across assembled system boundaries, `build` when runnable output is affected, and `smoke` when minimum viability of the built/running artifact must be proven.

For UI changes, also validate applicable states, accessibility, layout contexts, critical journeys and design-system consistency. A happy-path screenshot alone is not production-ready experience evidence.

## Security and data

See [`SECURITY.md`](SECURITY.md) and architecture/data-lifecycle documentation for trust boundaries, sensitive data handling and reporting. E2E/visual logs/screenshots/traces must remain privacy-safe and bounded-retention evidence.

## Development state

See [`docs/current-state.md`](docs/current-state.md). Active implementation coordination lives only in [`docs/workstreams/`](docs/workstreams/); completed implementation plans are deleted by default after durable knowledge transfer.
