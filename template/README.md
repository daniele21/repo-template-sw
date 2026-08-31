# <PROJECT_NAME>

<REPLACE_WITH_A_SHORT_PRODUCT_OR_LIBRARY_DESCRIPTION>

## Why this exists

<REPLACE_WITH_THE_PRIMARY_USER_OR_SYSTEM_OUTCOME>

## Architecture

See [`docs/architecture.md`](docs/architecture.md) for current boundaries and ownership. Keep this README focused on purpose, setup and public usage rather than implementation history.

Documentation ownership and change-impact rules live in [`docs/README.md`](docs/README.md). In particular, title/summary/`Why this exists` are README **identity** and should change only when the project's core purpose, primary audience or primary outcome changes. Setup/run/use/configuration/examples are README **usage** and must stay current whenever those interfaces change.

## Project commands

The canonical setup/dev/validation/build/package/cleanup mapping lives in [`.engineering/commands.json`](.engineering/commands.json). It exposes `setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop` and `clean` while keeping this project's native tooling underneath.

E2E applicability, target environments, execution environments, fidelity gaps and critical-journey mappings live in [`.engineering/e2e.json`](.engineering/e2e.json). Execution capability and environment fidelity are separate: an automated emulator/simulator run does not become physical/target-environment evidence merely because CI executed it.

For every UI-bearing critical E2E journey, complete run evidence requires both stable screenshot checkpoints and a complete journey video as identity-bearing, privacy-safe bounded artifacts. Assertions passing without either artifact class is incomplete E2E evidence.

Do not add a second undocumented command or E2E-environment truth for the same intent.

## Product experience

If `.engineering/baseline.json` includes `product-ui`, the canonical project experience/brand contracts live in [`design/ux-contract.json`](design/ux-contract.json) and [`design/brand-kit.json`](design/brand-kit.json).

They define or point to information hierarchy, progressive disclosure, critical states/journeys, accessibility, adaptive layout, brand/design tokens, component ownership and key reference views. They do not require one design tool or visual style.

If `product-ui` is not adopted, this section/design baseline is not applicable and may be removed during specialization.

## Setup

<REPLACE_WITH_THE_SHORTEST_RELIABLE_INSTALL_OR_BOOTSTRAP_PATH_FOR_A_NEW_USER_OR_DEVELOPER>

Use the command declared as `setup` in `.engineering/commands.json`, then `doctor` when environment diagnostics are needed. Keep prerequisites, required versions and first-run steps here current with the repository.

## Run

<REPLACE_WITH_THE_SHORTEST_RELIABLE_START_OR_LAUNCH_PATH>

Use the declared `dev` command when applicable. Local servers/processes must follow the repository's runtime/cleanup contract and leave no project-owned listeners/processes after stop.

## Use

<REPLACE_WITH_THE_SHORTEST_SUCCESSFUL_PUBLIC_USAGE_PATH: CLI/API/UI FLOW OR COPY-PASTE EXAMPLE>

Document the normal user/developer path, required inputs and the smallest useful example here. Link to deeper feature/API documentation rather than duplicating large contracts. If a feature change makes this path incomplete, wrong or misleading, update this section in the same change.

## Configuration

<REPLACE_WITH_PUBLIC_CONFIGURATION_THAT_A_NORMAL_USER_OR_DEVELOPER_MUST_KNOW, OR_REMOVE_THIS_SECTION_IF_NOT_APPLICABLE>

Document supported public options/defaults and required environment/configuration inputs. Internal implementation knobs that are not part of supported usage belong with their canonical technical owner instead.

## Build and artifacts

Use the declared `build`/`package` commands. Material builds use unique build identity, immutable successful artifacts, manifests/checksums, bounded local retention and a generated build delta against the previous successful comparable build.

## Validate

Start with repository health checks:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_operations.py
python3 scripts/verify_e2e.py
python3 scripts/verify_product_experience.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

`verify_e2e.py` validates the declared E2E target/execution environment and critical-journey contract, including the principle that UI journeys require screenshot + video artifacts. `verify_product_experience.py` passes as `N/A` unless `product-ui` is adopted.

Then use `check`/`test` while iterating. Use `e2e` when a critical complete workflow must be proven across assembled system boundaries, selecting the cheapest sufficient automated environment declared in `.engineering/e2e.json` and escalating fidelity only when the claim depends on a missing target dimension. For UI-bearing E2E, verify both screenshot checkpoints and complete-video artifacts before calling the journey evidence complete. Use `build` when runnable output is affected, and `smoke` when minimum viability of the built/running artifact must be proven.

Final device/manual/target-environment validation should primarily confirm the residual fidelity gaps that could not be reproduced earlier. Do not promote emulator/simulator evidence into a stronger physical/target claim.

For UI changes, also validate applicable states, accessibility, layout contexts, critical journeys and design-system consistency. Screenshot/video run artifacts make the executed flow inspectable but do not replace broader experience evidence when those claims are applicable.

## Security and data

See [`SECURITY.md`](SECURITY.md) and architecture/data-lifecycle documentation for trust boundaries, sensitive data handling and reporting. E2E/visual logs/screenshots/videos/traces must remain privacy-safe and bounded-retention evidence.

## Development state

See [`docs/current-state.md`](docs/current-state.md). Active implementation coordination lives only in [`docs/workstreams/`](docs/workstreams/); completed implementation plans are deleted by default after durable knowledge transfer.
