# Contributing

## Change scope

Prefer the smallest coherent change that preserves repository invariants. Inspect the owning boundary, direct consumers and tests before changing shared contracts.

Use a workstream plan only when dependency/state coordination adds real value. Small changes should not create planning documents.

## Canonical project commands

`.engineering/commands.json` is the canonical repository-level mapping for `setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop` and `clean`.

Use the project's native tooling behind those intents. Do not introduce a second undocumented build/test/E2E/run path merely for convenience.

## Product experience changes

When `product-ui` is adopted, `design/ux-contract.json` and `design/brand-kit.json` are canonical experience/brand routing surfaces.

Before a meaningful UI change, inspect the applicable user task, information hierarchy, progressive-disclosure level, critical states, accessibility/adaptive behavior, design-system owner and critical-journey evidence.

Prefer reusing an existing semantic component/token over creating a visually similar one-off. Do not expose advanced/debug complexity at the primary interaction level without a real user need.

## Validation

Run the narrowest useful checks while iterating, then the required integration/repository gates for the changed blast radius. Do not suppress failing tests or weaken a gate merely to make a change green.

Run repository health checks before publishing engineering-governance changes:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_operations.py
python3 scripts/verify_product_experience.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

Use `.engineering/commands.json` for actual project `check`/`test`/`e2e`/`build`/`smoke` commands.

Use E2E only when a complete critical user/system outcome needs to be proven across assembled boundaries and lower-level tests are insufficient. `smoke` proves minimum built/runtime viability and is not a substitute for E2E.

For UI changes, validate only the experience layers relevant to the claim: component/state behavior, critical-journey E2E, accessibility, adaptive layout, visual regression for stable high-risk surfaces, and usability evidence when the risk/value justifies it. A happy-path screenshot alone is not sufficient.

When E2E runs, verify cleanup of project-owned servers/listeners, browser/device sessions, test data, downloads/temp state and generated evidence. Failure traces/screenshots/videos/logs must have bounded retention and remain privacy-safe.

When build/runtime/package behavior changes, validate applicable operating invariants: unique build identity, immutable/promoted artifacts, manifest/checksum/build delta, bounded local retention, graceful stop and zero project-owned process/listener/temp residue.

## Dependencies and architecture

Avoid dynamic versions and speculative dependencies. New abstractions/dependencies must have a concrete owner/problem and should not duplicate an existing source of truth. Do not add an E2E, UI or design framework merely for compliance aesthetics or when an equally strong established mechanism already exists.

## Pull requests

Keep PRs focused. Describe what changed, why, user/developer impact, relevant failure/resource/operating/experience implications, and validation executed. Distinguish unit/integration/E2E/smoke/accessibility/visual/usability evidence and do not claim hardware/device/user evidence that was not run.

Canonical branches should be protected with pull requests and required checks according to the project's branching/release model.
