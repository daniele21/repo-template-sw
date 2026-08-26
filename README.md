# repo-template-sw

Agent-native reference engineering baseline for software repositories maintained by humans and coding agents.

`repo-template-sw` is not an application framework, universal build system or visual framework. It is the canonical source for a small engineering standard, reusable project bootstrap, common project operating contract, validation execution-capability contract, optional product-experience contract, coding-agent Skills, documentation/context governance, and deterministic repository health checks.

## Start here

- [`USAGE.md`](USAGE.md) — practical guide for using this repository with a brand-new project, an existing repository, ordinary coding-agent development, audits and baseline upgrades.
- [`STANDARD.md`](STANDARD.md) — canonical L0/L1/L2 engineering standard.
- [`OPERATING-CONTRACT.md`](OPERATING-CONTRACT.md) — common stack-neutral semantics for setup/dev/test/E2E/build/smoke/package/cleanup, build identity, artifacts and local runtimes.
- [`EXECUTION-CAPABILITY-CONTRACT.md`](EXECUTION-CAPABILITY-CONTRACT.md) — determines whether required validation runs agent-local, through remote automation, or in a genuine real environment; explicitly prevents humans from becoming fallback runners for automatable gates.
- [`PRODUCT-EXPERIENCE-CONTRACT.md`](PRODUCT-EXPERIENCE-CONTRACT.md) — optional stack-neutral UX/UI contract for products with a material user interface, including ordered product-experience decision semantics.

## What it optimizes for

- software correctness and explicit ownership;
- bounded memory/resources, concurrency and failure behavior;
- reproducible builds and validation;
- strong deterministic validation without requiring the repository owner to manually run commands an agent cannot execute locally;
- agent-triggerable remote preflight when supported coding agents lack the required shell/SDK/toolchain;
- layered unit/integration/E2E/smoke evidence matched to the strength of the claim;
- consistent project operations without forcing identical tooling;
- uniquely identifiable builds and traceable immutable artifacts;
- bounded artifact/cache/log/test-evidence retention and zero-residue runtime/build/E2E lifecycles;
- clear, accessible and progressively disclosed product experiences when UI is present;
- user-outcome-first UX reasoning before layout, motion and visual polish;
- purposeful motion/graphics without forcing one visual language;
- stable brand/design-system ownership without forcing one visual style;
- privacy/security and data lifecycle clarity;
- low repository/documentation entropy;
- low coding-agent context and token cost;
- safe parallel work through dependency-aware workstreams;
- reusable operating procedures without stuffing `AGENTS.md` with every rule.

The core principles are:

> **Make ownership, limits, failures and costs explicit, using the simplest solution that preserves the required invariants.**

> **Every operation must be identifiable, owned, bounded, reversible and leave no unintended residue.**

> **Automation executes automatable work; humans make material decisions and provide evidence that genuinely requires a real environment.**

> **A strong interface makes the user's next decision obvious, reveals complexity progressively, communicates system state clearly, and remains consistent, accessible and recoverable.**

> **UX before UI. Interaction before motion. Structure before polish. Evidence before completion.**

## Repository layout

- [`STANDARD.md`](STANDARD.md) — canonical L0/L1/L2 engineering standard.
- [`OPERATING-CONTRACT.md`](OPERATING-CONTRACT.md) — normative project command/test/E2E/build/artifact/runtime lifecycle semantics.
- [`EXECUTION-CAPABILITY-CONTRACT.md`](EXECUTION-CAPABILITY-CONTRACT.md) — normative executor classification and no-human-runner semantics.
- [`PRODUCT-EXPERIENCE-CONTRACT.md`](PRODUCT-EXPERIENCE-CONTRACT.md) — normative UX/UI semantics for repositories that adopt `product-ui`.
- [`USAGE.md`](USAGE.md) — practical adoption and operating guide.
- [`template/`](template/) — universal files that can be adopted into a project and then specialized locally.
- [`template/.engineering/commands.json`](template/.engineering/commands.json) — machine-readable project operating, publication and execution-routing contract template.
- [`template/design/`](template/design/) — opt-in UX/brand contract files validated when `product-ui` is adopted.
- [`template/skills/`](template/skills/) — core project-local coding-agent Skills, including `preflight-change` and `remote-preflight`.
- [`profiles/`](profiles/) — optional stack/domain/product guidance, including `product-ui` for UI products.
- [`skills/adopt-engineering-standard/`](skills/adopt-engineering-standard/) — workflow for aligning a new/existing repository.
- [`skills/update-engineering-standard/`](skills/update-engineering-standard/) — workflow for migrating an adopted repository to a newer baseline.

## Core model

```text
AGENTS.md          -> how to orient and what is invariant
commands.json      -> how this project runs/checks/tests/builds and where validation executes
Skills             -> how to perform recurring change/preflight workflows
remote preflight   -> agent-triggerable deterministic execution when local capability is missing
design contracts   -> how a UI product expresses users/jobs, hierarchy, motion semantics and brand/design ownership
Active workstream  -> what is being implemented now
Feature/ADR/docs   -> how the system works now and why durable decisions exist
Git history        -> how the repository got here
Scripts/CI         -> deterministic enforcement and remote execution
```

The operating model is **same semantics, native implementation**. Android remains Gradle/native-test-tooling, macOS remains Xcode/Swift/Python-native, browser/web can prefer Playwright for new browser E2E, and local servers use established tooling.

The validation execution model is **same required evidence, earliest capable automated executor**. If the coding agent has the correct local environment, it runs deterministic gates before CI confirmation. If it does not, repository-owned remote automation executes those gates. A human is not the default fallback runner.

The product-experience model follows the same rule: **same UX quality/decision contract, platform-appropriate implementation**. A web app, Android app and macOS app should converge on user-outcome-first task modeling, clarity, progressive disclosure, complete states, accessibility, purposeful motion/graphics and design-system ownership without being forced into identical visuals or interactions.

For meaningful product-experience work, the default decision order is:

```text
user outcome
-> task model
-> IA / critical journey
-> information + action hierarchy
-> progressive disclosure / defaults
-> interactions / states / feedback / recovery
-> adaptive / platform
-> accessibility
-> design system / components
-> motion
-> visual polish / graphics
-> validation
```

Use proportional depth: structural UX changes use the full sequence; interaction changes start from the owning task/journey and affected layers; visual-only edits preserve settled semantics and remain local.

## Use with a new project

1. Read [`USAGE.md`](USAGE.md), `STANDARD.md` and the applicable focused contracts.
2. Copy the universal `template/` baseline into the repository.
3. Select only the profiles that apply.
4. If the product has a material UI, adopt `product-ui` and specialize `design/ux-contract.json` plus `design/brand-kit.json`.
5. Replace project placeholders and generate project-specific ownership/routing.
6. Map `.engineering/commands.json` to native setup/dev/check/test/E2E/build/smoke/package/stop/clean commands.
7. Decide which required gates can run agent-local, which need remote automation, and which genuinely require a real environment.
8. If supported coding agents may lack required local tooling, provide the declared agent-triggerable remote-preflight mechanism before relying on them for autonomous delivery.
9. Decide E2E applicability and cover only critical whole-system workflows lower-level tests cannot prove.
10. Implement applicable build identity, artifact lifecycle/build-delta and local-runtime/cleanup semantics.
11. For UI products, identify primary users/jobs/surfaces, define information architecture/journeys, progressive disclosure, critical states, accessibility target, responsive/adaptive scope, design-system ownership, motion/graphics semantics and key reference views; route meaningful UX/UI work through `design-product-experience`.
12. Record adopted standard version and profiles in `.engineering/baseline.json`.
13. Run repository/operations/product-experience/documentation/agent-context checks.
14. Add stack-specific automated/test/E2E/build/smoke and UI evidence gates before claiming the relevant maturity level.

The `adopt-engineering-standard` Skill describes the complete workflow.

## Use with an existing project

Do not copy blindly. First audit existing architecture, docs, CI, tests, E2E framework/critical journeys, security, native commands, build/version identity, artifact/release behavior, local runtimes, cleanup and agent guidance.

Also audit execution capability: determine whether the coding agents expected to maintain the repository can run required deterministic gates directly. Where they cannot, preserve or add secure agent-triggerable remote automation rather than assigning those commands to the repository owner.

For UI products also audit primary users/jobs, information architecture/journeys, progressive disclosure, design source of truth, brand tokens/components, critical states, accessibility, responsive/adaptive behavior, motion/imagery ownership, key reference views and UX regression evidence. Preserve stronger existing practices, identify gaps/conflicts, build a small adoption DAG, then migrate incrementally.

The goal is convergence on engineering and experience invariants, not identical repository layouts, build tools, E2E frameworks or visual styles.

## Updating an adopted project

Projects remain self-contained. They do not depend on this repository at runtime or during ordinary coding-agent tasks.

When this baseline changes, compare the project's recorded standard version with the desired version, identify relevant semantic deltas, preserve local customizations/native tooling/design systems, and apply a focused migration. See `skills/update-engineering-standard` and [`USAGE.md`](USAGE.md).

## Documentation lifecycle

Implementation plans are disposable by default:

```text
plan -> implement -> validate -> transfer durable knowledge -> delete plan
```

Git already preserves implementation history. Keep completed plans only when they have independent audit, regulatory, release or historical value. Generated per-build deltas and per-run E2E/visual evidence are artifacts, not active planning documents.

## Versioning

The baseline version is stored in [`VERSION`](VERSION). Changes that alter required invariants, copied Skills or machine-readable baseline semantics must be recorded in [`CHANGELOG.md`](CHANGELOG.md).

Current baseline: **0.7.0**.
