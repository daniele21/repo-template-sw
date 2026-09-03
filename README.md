# repo-template-sw

Agent-native reference engineering baseline for software repositories maintained by humans and coding agents.

`repo-template-sw` is not an application framework or universal build system. It defines a compact engineering standard, reusable adopter template, operating/development-velocity contracts, E2E environment/evidence semantics, optional product-experience semantics, coding-agent Skills and deterministic repository health checks.

## Start here

- [`USAGE.md`](USAGE.md) — practical adoption, ordinary development and migration guide.
- [`STANDARD.md`](STANDARD.md) — canonical L0/L1/L2 engineering standard.
- [`OPERATING-CONTRACT.md`](OPERATING-CONTRACT.md) — command/build/artifact/runtime lifecycle semantics.
- [`EXECUTION-CAPABILITY-CONTRACT.md`](EXECUTION-CAPABILITY-CONTRACT.md) — delivery stages, risk-based validation, executor routing and evidence reuse.
- [`E2E-ENVIRONMENT-CONTRACT.md`](E2E-ENVIRONMENT-CONTRACT.md) — target-environment/fidelity semantics and risk-based UI evidence modes.
- [`PRODUCT-EXPERIENCE-CONTRACT.md`](PRODUCT-EXPERIENCE-CONTRACT.md) — optional stack-neutral UX/UI contract.

## 0.9.x: Development Velocity

The delivery model is explicitly two-dimensional:

```text
Delivery stage:     ITERATION -> INTEGRATION -> RELEASE
Validation depth:   LEAN | SCOPED | STRONG | FULL
```

They answer different questions.

- **Stage** says *when* the change is in its lifecycle.
- **Depth** says *how much evidence* the actual risk requires.

### ITERATION

Default while implementation is changing. Optimize for a short feedback loop: touched formatter/static checks, affected compile/typecheck, focused tests and direct contract tests when needed.

Do not automatically require exact-head publication evidence, complete-diff review, durable-documentation freshness, remote preflight, release packaging, broad emulator suites or E2E media for every edit.

### INTEGRATION

Begins when a coherent **vertical outcome** is ready to converge. Refresh exact head/base, inspect the complete diff, update affected durable docs, select concrete risk gates and run the smallest required critical E2E.

### RELEASE

Promotion/release/reference checkpoints use `FULL` validation plus release-critical build/package/E2E and residual real-environment evidence.

The objective is:

> **high-confidence incremental delivery without validation waterfall.**

## What it optimizes for

- explicit ownership and simple architecture;
- bounded resources/concurrency/failure behavior;
- fast deterministic feedback during implementation;
- risk-to-gate validation instead of full-suite-by-default;
- agent-triggerable remote automation when local tooling is unavailable;
- reuse of equivalent successful validation evidence instead of duplicate runs;
- critical E2E matched to both claim strength and environment fidelity;
- risk-based UI E2E evidence: `ASSERTIONS`, `SCREENSHOTS`, `FULL_MEDIA`;
- reproducible builds and immutable traceable artifacts;
- zero-residue runtime/build/E2E lifecycles;
- low repository/documentation entropy and low agent context cost;
- observable vertical slices and early convergence of parallel work;
- clear, accessible and progressively disclosed product experiences where UI exists.

Core principles:

> **Make ownership, limits, failures and costs explicit, using the simplest solution that preserves the required invariants.**

> **Optimize for sufficient confidence per unit of feedback time.**

> **Automation executes automatable work; humans make material decisions and provide genuinely real-environment evidence.**

> **Final target-environment validation confirms residual fidelity gaps rather than becoming the first complete-system test.**

## Validation model

The selector should resolve:

```text
changed outcome
-> risk dimensions
-> required gates
-> LEAN | SCOPED | STRONG | FULL summary
-> AGENT_LOCAL | REMOTE_AUTOMATED | REAL_ENVIRONMENT executor
```

Profiles are shorthand, not monolithic suite aliases.

Typical examples:

- contained UI/ViewModel/domain change -> `SCOPED` owner/direct-consumer gates;
- Binder/shared contract, persistence, lifecycle, native/JNI, packaging/R8/manifest -> relevant `STRONG` risk cone;
- selector/global build/toolchain/dependency inventory -> `FULL` because narrowing machinery changed;
- stable/release promotion -> `FULL`.

## Remote preflight and evidence reuse

Before starting another expensive run, reuse successful evidence when it still proves the required claim.

For an integration candidate, evidence normally matches:

- exact source head;
- source Git tree when available;
- material target/base relationship;
- required gates;
- selected profile or stronger equivalent;
- E2E environment/fidelity/evidence mode where relevant.

PR number, draft/ready state, labels and comments are not source-evidence identity by themselves. A replacement PR with the same head/base/gates should not rerun unchanged validation solely because its UI identity changed.

After a content-preserving squash/rebase into an integration branch, post-merge CI may reuse the green candidate evidence even though the commit SHA changed **only** when the final Git tree is identical and the push base is exactly the target/base used by the validation. A moved base, changed tree, broader gate set, direct push without trusted evidence or release candidate falls back to normal validation.

This distinction preserves exact-head integration proof while avoiding a second expensive run caused only by commit-history metadata.

## E2E model

Execution capability and environment fidelity are independent.

```text
host/fake
-> simulator/emulator
-> representative virtual
-> representative physical
-> target environment
```

Use the cheapest automated environment that proves the claim and escalate only for material target dimensions.

For UI-bearing critical journeys, select evidence from the changed claim:

- `ASSERTIONS` — UI is incidental to deterministic system behavior;
- `SCREENSHOTS` — stable visible layout/hierarchy/copy/state/recovery/adaptive behavior changed;
- `FULL_MEDIA` — motion, timing/progression, navigation/transition sequence, lifecycle visibility, gesture continuity or release acceptance requires observing sequence over time.

UI presence alone does not force video. Evidence required by the selected mode must be complete and identity-bearing.

## Vertical slices and parallel work

Prefer an observable user/system outcome over a small technical layer.

```text
agent A subtask ─┐
agent B subtask ─┼─> feature/integration branch -> coherent vertical-slice PR
agent C subtask ─┘
```

Parallel development does not imply stacked publication. Stacked PRs remain useful when levels are independently mergeable/reviewable/value-bearing, but sync-only stack-maintenance PRs are a smell.

## Repository layout

- `STANDARD.md` — universal engineering standard.
- `OPERATING-CONTRACT.md` — operations/build/artifact/runtime contract.
- `EXECUTION-CAPABILITY-CONTRACT.md` — development velocity, validation/executor/evidence-reuse contract.
- `E2E-ENVIRONMENT-CONTRACT.md` — E2E environment/evidence contract.
- `PRODUCT-EXPERIENCE-CONTRACT.md` — optional UX/UI contract.
- `template/` — files adopted and then specialized locally.
- `template/.engineering/commands.json` — machine-readable commands, stages, validation routing and preflight policy.
- `template/.engineering/e2e.json` — machine-readable E2E environments, journeys and UI evidence policy.
- `template/skills/` — recurring project-local agent workflows.
- `profiles/` — optional stack/domain/product deltas such as Android/local-AI/product-ui.
- `skills/adopt-engineering-standard/` — first adoption.
- `skills/update-engineering-standard/` — explicit baseline migration.

## Use with a new project

1. Read `USAGE.md`, `STANDARD.md` and only the focused contracts/profiles that apply.
2. Copy/specialize `template/`.
3. Select only applicable profiles.
4. Map `.engineering/commands.json` to the project's native commands and specialize `development_velocity`/risk selector/remote-preflight routing.
5. Decide E2E applicability and specialize `.engineering/e2e.json` with target/execution environments, critical journeys, residual gaps and minimum UI evidence modes.
6. If UI is material, adopt `product-ui` and map the real design-system/brand owner.
7. Preserve stronger existing tooling rather than replacing it for compliance aesthetics.
8. Record baseline version/profiles and run repository health checks.

## Use with an existing project

Adoption is semantic, not file replacement. Preserve stronger local ownership, CI, E2E, build/release and design mechanisms; merge only missing invariants/routing.

Later upgrades use `update-engineering-standard`: read the version delta, classify each change as APPLY/MERGE/N/A/DEFER/CONFLICT, preserve intentional customization and bump baseline metadata only after the new behavior is real.

## Current version

Reference baseline: **0.9.1**.

See [`CHANGELOG.md`](CHANGELOG.md) for the adopter-facing delta.
