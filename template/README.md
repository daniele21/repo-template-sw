# <PROJECT_NAME>

<REPLACE_WITH_A_SHORT_PRODUCT_OR_LIBRARY_DESCRIPTION>

## Why this exists

<REPLACE_WITH_THE_PRIMARY_USER_OR_SYSTEM_OUTCOME>

## Architecture

See [`docs/architecture.md`](docs/architecture.md) for current boundaries and ownership. Keep this README focused on purpose, setup and public usage rather than implementation history.

Documentation ownership and change-impact rules live in [`docs/README.md`](docs/README.md). Title/summary/`Why this exists` are README **identity** and should change only when the project's core purpose, primary audience or primary outcome changes. Setup/run/use/configuration/examples are README **usage** and must stay current whenever those interfaces change.

## Project commands and delivery stages

The canonical setup/dev/validation/build/package/cleanup mapping lives in [`.engineering/commands.json`](.engineering/commands.json). It exposes `setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop` and `clean` while keeping this project's native tooling underneath.

The same file declares delivery stages separately from validation depth:

- `ITERATION` — fast owner-focused feedback while implementation changes;
- `INTEGRATION` — exact-head vertical-slice readiness with complete diff, affected durable docs and required risk gates;
- `RELEASE` — full release/reference-grade validation.

`LEAN`, `SCOPED`, `STRONG` and `FULL` summarize validation depth; they are not delivery stages.

E2E applicability, target environments, execution environments, fidelity gaps and critical journeys live in [`.engineering/e2e.json`](.engineering/e2e.json).

For UI-bearing journeys, evidence is risk-based:

- `ASSERTIONS` when UI is incidental to a non-visual system claim;
- `SCREENSHOTS` when stable visible states/layout/hierarchy/copy/recovery/adaptive semantics changed;
- `FULL_MEDIA` when motion, timing/progression, navigation/transition sequencing, lifecycle visibility, gesture continuity or release acceptance requires observing the journey over time.

UI presence alone does not force video. Missing evidence required by the selected mode means incomplete E2E evidence.

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

Document the normal user/developer path, required inputs and the smallest useful example here. Link to deeper feature/API documentation rather than duplicating large contracts. If a feature change makes this path incomplete, wrong or misleading, update this section when the coherent slice moves to integration.

## Configuration

<REPLACE_WITH_PUBLIC_CONFIGURATION_THAT_A_NORMAL_USER_OR_DEVELOPER_MUST_KNOW, OR REMOVE_THIS_SECTION_IF_NOT_APPLICABLE>

Document supported public options/defaults and required environment/configuration inputs. Internal implementation knobs that are not part of supported usage belong with their canonical technical owner instead.

## Build and artifacts

Use the declared `build`/`package` commands. Material builds use unique build identity, immutable successful artifacts, manifests/checksums, bounded local retention and a generated build delta against the previous successful comparable build.

## Validate

Start with repository health checks when engineering-governance files change:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_operations.py
python3 scripts/verify_e2e.py
python3 scripts/verify_product_experience.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

`verify_e2e.py` validates E2E target/execution environments, critical journeys and risk-based UI evidence policy. `verify_product_experience.py` passes as `N/A` unless `product-ui` is adopted.

During `ITERATION`, use the cheapest `check`/focused test/compile gates that can falsify the current edit. Do not run full repository/release validation mechanically.

At `INTEGRATION`, select concrete risk gates, make affected durable docs current, reuse equivalent successful validation evidence where valid, and run the smallest necessary E2E journey/environment/evidence mode.

At `RELEASE`, run full release-critical validation and artifact/E2E evidence.

Final device/manual/target-environment validation should primarily confirm residual fidelity gaps that could not be reproduced earlier. Do not promote emulator/simulator evidence into a stronger physical/target claim.

For UI changes, validate applicable states, accessibility, layout contexts, critical journeys and design-system consistency. Media evidence makes the executed flow inspectable but does not replace broader experience evidence when those claims apply.

## Security and data

See [`SECURITY.md`](SECURITY.md) and architecture/data-lifecycle documentation for trust boundaries, sensitive data handling and reporting. E2E/visual logs/screenshots/videos/traces must remain privacy-safe and bounded-retention evidence.

## Development state

See [`docs/current-state.md`](docs/current-state.md) for integrated/blocked/next repository truth. Active implementation coordination lives only in [`docs/workstreams/`](docs/workstreams/); completed implementation plans are deleted by default after durable knowledge transfer.
