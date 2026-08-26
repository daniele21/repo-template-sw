# Contributing

## Change scope

Prefer the smallest coherent change that preserves repository invariants. Inspect the owning boundary, direct consumers and tests before changing shared contracts.

Use a workstream plan only when dependency/state coordination adds real value. Small changes should not create planning documents.

Resolve material ambiguity from canonical repository evidence before implementation. If two reasonable interpretations remain and would materially alter product behavior, public contracts, persistence/migration, security/trust, failure/resource/lifecycle semantics, compatibility, acceptance criteria or meaningful UX, ask the user/owner instead of silently selecting one.

## Canonical project commands

`.engineering/commands.json` is the canonical repository-level mapping for `setup`, `doctor`, `dev`, `check`, `test`, `e2e`, `build`, `smoke`, `package`, `stop` and `clean`, and declares publication readiness, validation execution classes and remote-preflight routing.

Use the project's native tooling behind those intents. Do not introduce a second undocumented build/test/E2E/run path merely for convenience.

## Product experience changes

When `product-ui` is adopted, `design/ux-contract.json` and `design/brand-kit.json` are canonical experience/brand routing surfaces.

Before a meaningful UI change, inspect the applicable user task, information hierarchy, progressive-disclosure level, critical states, accessibility/adaptive behavior, design-system owner and critical-journey evidence.

Prefer reusing an existing semantic component/token over creating a visually similar one-off. Do not expose advanced/debug complexity at the primary interaction level without a real user need.

## Validation

Run the narrowest useful checks while iterating, then the required integration/repository gates for the changed blast radius. Do not suppress failing tests or weaken a gate merely to make a change green.

When a gate fails, classify it before changing production code: current-change regression, baseline failure, environment/toolchain issue, flaky behavior, stale-base effect or incorrect assumption/contract. Fix the owning invariant rather than applying unexplained symptom patches. Repeated failure of the same gate after a fix requires re-evaluating the hypothesis before another patch.

Run repository health checks before publishing engineering-governance changes:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_operations.py
python3 scripts/verify_product_experience.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

Use `.engineering/commands.json` for actual project `check`/`test`/`e2e`/`build`/`smoke` commands.

For each required final gate, classify execution for the current agent/session as `AGENT_LOCAL`, `REMOTE_AUTOMATED` or `REAL_ENVIRONMENT`.

- Run `AGENT_LOCAL` gates directly.
- Use `skills/remote-preflight/SKILL.md` for deterministic `REMOTE_AUTOMATED` gates.
- Reserve `REAL_ENVIRONMENT` for evidence that genuinely requires representative hardware, external authority/environment or manual judgement.

Do not ask the user to run an automatable deterministic command merely because the current coding agent lacks a checkout, shell, SDK or platform toolchain. Missing remote execution for such a gate is an automation-capability gap to fix, not a permanent human task.

Use E2E only when a complete critical user/system outcome needs to be proven across assembled boundaries and lower-level tests are insufficient. `smoke` proves minimum built/runtime viability and is not a substitute for E2E.

For UI changes, validate only the experience layers relevant to the claim: component/state behavior, critical-journey E2E, accessibility, adaptive layout, visual regression for stable high-risk surfaces, and usability evidence when the risk/value justifies it. A happy-path screenshot alone is not sufficient.

When E2E runs, verify cleanup of project-owned servers/listeners, browser/device sessions, test data, downloads/temp state and generated evidence. Failure traces/screenshots/videos/logs must have bounded retention and remain privacy-safe.

When build/runtime/package behavior changes, validate applicable operating invariants: unique build identity, immutable/promoted artifacts, manifest/checksum/build delta, bounded local retention, graceful stop and zero project-owned process/listener/temp residue.

## Pre-publication readiness

Before pushing/opening/updating a PR, use `skills/preflight-change/SKILL.md` on the exact head.

If every required deterministic gate can run in the current agent environment and passes, record `READY_FOR_CI` and use CI as independent confirmation.

If required deterministic gates are automatable but unavailable to the current agent, record `READY_FOR_REMOTE_PREFLIGHT` after semantic/base/diff and available local checks pass, then use `skills/remote-preflight/SKILL.md` to trigger repository-owned remote automation. The agent should inspect failures, fix the owning cause and retrigger without delegating the loop to the user.

`AUTOMATED_PREFLIGHT_CONFIRMED` requires every required deterministic automated gate to pass on the exact head/base. Real-environment evidence may remain explicitly pending and still blocks stronger claims that depend on it.

## Dependencies and architecture

Avoid dynamic versions and speculative dependencies. New abstractions/dependencies must have a concrete owner/problem and should not duplicate an existing source of truth. Do not add an E2E, UI or design framework merely for compliance aesthetics or when an equally strong established mechanism already exists.

## Pull requests

Keep PRs focused. Describe what changed, why, user/developer impact, relevant failure/resource/operating/experience implications, and validation executed. Distinguish agent-local, remote-automated and real-environment evidence and do not claim hardware/device/user evidence that was not run.

Record preflight head/base identity and the actual readiness state: `READY_FOR_CI`, `READY_FOR_REMOTE_PREFLIGHT`, `AUTOMATED_PREFLIGHT_CONFIRMED` or a blocked state. A known-red draft may be published for explicit collaboration/investigation, but must not be represented as ready.

Canonical branches should be protected with pull requests and required checks according to the project's branching/release model.
