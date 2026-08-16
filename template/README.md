# <PROJECT_NAME>

<REPLACE_WITH_A_SHORT_PRODUCT_OR_LIBRARY_DESCRIPTION>

## Why this exists

<REPLACE_WITH_THE_PRIMARY_USER_OR_SYSTEM_OUTCOME>

## Architecture

See [`docs/architecture.md`](docs/architecture.md) for current boundaries and ownership. Keep this README focused on purpose, setup and public usage rather than implementation history.

## Project commands

The canonical setup/dev/validation/build/package/cleanup mapping lives in [`.engineering/commands.json`](.engineering/commands.json). It exposes the common intents `setup`, `doctor`, `dev`, `check`, `test`, `build`, `smoke`, `package`, `stop` and `clean` while keeping this project's native tooling underneath.

Do not add a second undocumented command path for the same intent.

## Setup

Use the command declared as `setup` in `.engineering/commands.json`, then `doctor` when environment diagnostics are needed.

## Run

Use the declared `dev` command when applicable. Local servers/processes must follow the repository's runtime/cleanup contract and leave no project-owned listeners/processes after stop.

## Build and artifacts

Use the declared `build`/`package` commands. Material builds use a unique build identity, immutable successful artifacts, manifests/checksums, bounded local retention and a generated build delta against the previous successful comparable build.

## Validate

Start with the repository engineering checks:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_operations.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

Then use the declared `check`, `test`, `build` and `smoke` intents according to the change's blast radius.

## Security and data

See [`SECURITY.md`](SECURITY.md) and the architecture/data-lifecycle documentation for trust boundaries, sensitive data handling and reporting.

## Development state

See [`docs/current-state.md`](docs/current-state.md). Active implementation coordination lives only in [`docs/workstreams/`](docs/workstreams/); completed implementation plans are deleted by default after durable knowledge transfer.
