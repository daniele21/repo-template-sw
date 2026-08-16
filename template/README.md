# <PROJECT_NAME>

<REPLACE_WITH_A_SHORT_PRODUCT_OR_LIBRARY_DESCRIPTION>

## Why this exists

<REPLACE_WITH_THE_PRIMARY_USER_OR_SYSTEM_OUTCOME>

## Architecture

See [`docs/architecture.md`](docs/architecture.md) for current boundaries and ownership. Keep this README focused on purpose, setup and public usage rather than implementation history.

## Setup

<REPLACE_WITH_REPRODUCIBLE_SETUP_STEPS_AND_PINNED_TOOLCHAIN_REQUIREMENTS>

## Run

<REPLACE_WITH_THE_SMALLEST_SUPPORTED_RUN_COMMANDS>

## Validate

Start with the repository engineering checks:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

Then run the stack-specific format/lint/static/test/build commands documented in `CONTRIBUTING.md` and `AGENTS.md`.

## Security and data

See [`SECURITY.md`](SECURITY.md) and the architecture/data-lifecycle documentation for trust boundaries, sensitive data handling and reporting.

## Development state

See [`docs/current-state.md`](docs/current-state.md). Active implementation coordination lives only in [`docs/workstreams/`](docs/workstreams/); completed implementation plans are deleted by default after durable knowledge transfer.
