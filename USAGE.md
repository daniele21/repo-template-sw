# Using `repo-template-sw`

This guide explains how to use `repo-template-sw` with a new repository, an existing repository, and an already-adopted repository that needs a baseline migration.

`repo-template-sw` is a **bootstrap, audit and migration source**. After adoption, ordinary work should be driven by the target repository itself.

The governing model is **same semantics, native implementation**: repositories converge on engineering and product-experience invariants without being forced onto the same build system, E2E framework, design tool or visual style.

## Mental model

```text
repo-template-sw
      |
      +--> STANDARD.md                    engineering maturity/invariants
      +--> OPERATING-CONTRACT.md          commands/E2E/build/artifact/runtime
      +--> PRODUCT-EXPERIENCE-CONTRACT.md UX/UI quality when applicable
      +--> template/                      adoptable baseline
      +--> profiles/                      stack/product mapping
      |
      +--> new repository      -> bootstrap + specialize
      +--> existing repository -> audit + preserve + align
      +--> adopted repository  -> explicit semantic migration

After adoption:

project repository
      |
      +--> AGENTS.md                    agent routing/invariants
      +--> .engineering/commands.json  canonical operations
      +--> design/*                    UX/brand contract if product-ui
      +--> local Skills                recurring workflows
      +--> active workstream           current coordinated work only
      +--> durable docs                current behavior/decisions
      +--> scripts / CI                deterministic enforcement
```

---

# 1. Common operating model

Every adopted application/runtime repository declares applicable intents in `.engineering/commands.json`:

```text
setup -> doctor -> dev -> check -> test -> e2e -> build -> smoke -> package -> stop -> clean
```

Do not add wrappers solely for naming consistency. Keep the repository's native Gradle/Xcode/Python/Node/etc. tooling underneath.

Keep validation layers distinct:

```text
unit/component
   ↓
integration/contract
   ↓
end-to-end critical journey
   ↓
smoke of built/running artifact
```

Use E2E only when the complete outcome cannot be proven adequately below that level. Browser/web may prefer Playwright when introducing a new E2E framework; native/mobile/server/CLI projects should use the strongest appropriate native/protocol-level tool.

Material builds use unique identity, immutable successful artifacts, manifests/checksums, generated build delta, bounded local retention and durable release storage. Runtime/test/E2E/build operations own and clean all processes, listeners, temp state and evidence they create.

---

# 2. Product experience model

Use the optional `product-ui` profile when the repository has a material user-facing interface.

Do not add it to headless libraries/APIs merely because every project uses the same baseline.

A `product-ui` project specializes:

```text
design/
├── ux-contract.json
├── brand-kit.json
└── README.md
```

The contract covers:

- user-task model rather than internal-architecture exposure;
- information architecture and clear primary-action hierarchy;
- progressive disclosure: essential -> contextual -> advanced -> expert/diagnostics;
- bounded cognitive load/information density;
- sensible defaults before deep configuration;
- complete loading/empty/error/disabled and other applicable states;
- immediate feedback, truthful progress and actionable recovery;
- accessibility target/evidence;
- responsive/adaptive behavior;
- semantic brand/design tokens;
- canonical component/design-system ownership;
- key reference views/mockups without screenshot-version sprawl;
- critical user journeys linked to E2E when needed;
- visual/accessibility/usability regression evidence appropriate to risk.

The UI should feel contemporary through hierarchy, typography, spacing, restraint, consistency and platform conventions. The standard intentionally does **not** require trend-specific treatments such as a particular card style, glass effect, gradient system or animation aesthetic.

## Brand kit

`design/brand-kit.json` is a small contract, not a replacement for Figma/code/design-system tooling. It records or points to the canonical product name, logo variants, app icon/favicon applicability, semantic colors, typography, spacing, radius/elevation, iconography, motion, imagery and microcopy principles.

Use semantic tokens rather than scattered raw values.

## Design source of truth

`design/ux-contract.json` declares whether the canonical design owner is Figma, code-first, in-repo design files or another explicit source.

Keep only deliberate key reference views. Generated visual-regression screenshots/traces/videos belong in bounded CI evidence, not normal source history.

## Accessibility

Web products should target WCAG 2.2 AA or a stronger declared target. Native products should use equivalent platform accessibility APIs/guidelines.

Automated accessibility tests are useful but do not replace manual keyboard/screen-reader/device validation where important.

---

# 3. Starting a repository from zero

Recommended request to a coding agent:

```text
Bootstrap <TARGET_REPOSITORY> using the current stable baseline of daniele21/repo-template-sw.

Use adopt-engineering-standard.

Before implementing product features:
1. identify product/runtime, languages, platforms, persistence, network/security, build/distribution and UI boundaries;
2. select only applicable profiles;
3. adopt and specialize the universal baseline;
4. if there is a material UI, add product-ui and specialize design/ux-contract.json and design/brand-kit.json;
5. create a project-specific AGENTS.md with real ownership/routing;
6. map .engineering/commands.json to native tooling;
7. decide E2E applicability and identify only critical full workflows lower-level tests cannot prove;
8. implement build identity, artifact lifecycle/build delta and zero-residue runtime behavior where applicable;
9. for product-ui, define IA, progressive disclosure, defaults, critical states/journeys, accessibility, adaptive layout, brand/design-system ownership and key reference views;
10. configure stack-specific format/lint/test/E2E/build/smoke/UI evidence gates;
11. record baseline version/profiles;
12. run repository, operating, product-experience, documentation and agent-context health checks;
13. report maturity truthfully.

Do not leave placeholders. Do not add profiles, wrappers, UI frameworks, E2E frameworks or design tooling without a real need.
```

Typical profile combinations:

```text
Python local inference service
-> python + local-ai

Android app
-> android + product-ui

Android local-AI app
-> android + local-ai + product-ui

macOS desktop app
-> macos + product-ui

macOS Python local-AI app
-> python + macos + local-ai + product-ui

TypeScript web app
-> typescript + product-ui

Headless Python API
-> python
```

Bootstrap creates structure and contracts; it does not itself prove L1/L2.

---

# 4. Aligning an existing repository

**Audit first. Do not copy `template/` blindly.**

Recommended audit request:

```text
Audit <TARGET_REPOSITORY> against the current stable baseline of daniele21/repo-template-sw.
Do not modify the repository yet.

Classify every relevant concern as KEEP / ADAPT / ADD / N/A / CONFLICT.

Inspect:
- architecture/ownership and AGENTS/Skills/docs;
- setup/dev/check/test/E2E/build/package/clean commands;
- build identity, artifacts, retention, releases and BUILD_CHANGELOG;
- localhost/process/port/temp cleanup;
- resource/concurrency/failure/security/data lifecycle;
- critical user/system journeys and E2E evidence;
- if a material UI exists: information architecture, progressive disclosure, defaults, action hierarchy, critical states, accessibility, adaptive layout, brand/design-system source of truth, key reference views and visual/usability regression evidence.

Return:
- current L0/L1/L2 estimate;
- KEEP / ADAPT / ADD / N/A / CONFLICT matrix;
- what should explicitly NOT change;
- highest-value gaps;
- a small dependency-aware adoption DAG;
- safe parallel lanes.
```

Then implement the approved adoption incrementally:

```text
Implement the approved repo-template-sw adoption plan.
Preserve KEEP items and native tooling/design systems.
Map existing semantics rather than replacing strong mechanisms.
Add only justified gaps.
Run affected health checks and real evidence at the strength of the claim.
Transfer durable truth and delete the temporary adoption workstream by default.
```

Examples:

```text
Strong existing design system/Figma source
-> KEEP; point ux-contract.json to it

Strong existing Playwright/XCUITest/Espresso suite
-> KEEP; map critical journeys to canonical e2e

Dense technical settings screen exposing all controls
-> ADAPT via progressive disclosure if normal users do not need simultaneous access

Missing loading/error/empty states
-> ADD

Scattered raw colors/components
-> ADAPT toward semantic tokens/canonical components

No meaningful UI
-> product-ui N/A

Server leaves port open after tests
-> CONFLICT with zero-residue invariant
```

---

# 5. Normal development after adoption

Ordinary work should use only the target repository:

```text
user request
    ↓
AGENTS.md
    ↓
closest scoped guide when needed
    ↓
commands.json for operations
    ↓
ux-contract/brand-kit when UI behavior changes
    ↓
local Skill + owning code/tests
```

A normal request can stay small:

```text
Implement memory-aware eviction.
```

or:

```text
Redesign model settings so advanced parameters are progressively disclosed.
```

The repository should already encode how to validate the work.

For UI changes, do not stop at a screenshot. Validate applicable behavior/states, accessibility, layout contexts, E2E critical journeys and design-system consistency according to the claim.

Use `plan-workstream` only when coordination/dependencies make it useful. Completed plans are deleted after durable knowledge transfer by default.

---

# 6. Updating an adopted repository

Baseline upgrades are explicit semantic migrations, not automatic file synchronization.

Recommended request:

```text
Migrate <TARGET_REPOSITORY> from its recorded repo-template-sw baseline to <TARGET_VERSION>.
Use update-engineering-standard.

Read baseline.json, commands.json, local Skills and design contracts when product-ui is adopted.
Compare VERSION/CHANGELOG and changed focused contracts.
Classify each delta APPLY / MERGE / N/A / DEFER / CONFLICT.
Preserve stronger local mechanisms and customizations.
Implement semantic changes, validate them, then update metadata.
```

For **0.3.x -> 0.4.0**, explicitly evaluate:

```text
Does this repo have a material UI?
  no  -> product-ui N/A; verifier should pass as not applicable
  yes -> classify product-ui adoption

If yes:
- canonical design/brand source of truth;
- semantic tokens/component ownership;
- information architecture and action hierarchy;
- progressive disclosure and sensible defaults;
- critical loading/empty/error/disabled states;
- feedback/error recovery;
- accessibility target/evidence;
- responsive/adaptive contexts;
- critical user journeys and E2E linkage;
- key reference views;
- visual/accessibility/usability regression strategy;
- zero-residue/bounded UI test evidence.
```

A metadata-only version bump is not a valid migration.

---

# 7. Using the standard as an audit tool

You can use the baseline without modifying a repository:

```text
Review <TARGET_REPOSITORY> against daniele21/repo-template-sw.
Do not change code.

Assess engineering maturity plus, when a material UI exists:
- clarity of task model/information architecture;
- progressive disclosure/cognitive load;
- sensible defaults/action hierarchy;
- complete states/feedback/recovery;
- accessibility;
- responsive/adaptive behavior;
- brand/design-system ownership;
- critical journeys/E2E/visual/usability evidence;
- design/mockup/source-of-truth hygiene.

Estimate L0/L1/L2 and rank the highest-value evidence-backed gaps.
```

---

# 8. What belongs where

| Concern | Canonical owner |
| --- | --- |
| Universal engineering invariant | `STANDARD.md` |
| Command/E2E/build/artifact/runtime semantics | `OPERATING-CONTRACT.md` |
| Universal UX/UI semantics | `PRODUCT-EXPERIENCE-CONTRACT.md` |
| Optional stack/product mapping | `profiles/` |
| Project operations | `.engineering/commands.json` |
| Project UX contract | `design/ux-contract.json` |
| Project brand/design-token contract | `design/brand-kit.json` |
| Actual design source | declared Figma/code/in-repo owner |
| Project routing/invariants | `AGENTS.md` |
| Recurring project procedure | `skills/` |
| Current coordinated implementation | active workstream |
| Current behavior | feature/architecture docs + code/tests |
| Durable decision | ADR |
| Implementation history | Git |
| Per-build exact delta | artifact `BUILD_CHANGELOG.md` |
| Generated E2E/visual evidence | bounded CI artifact store |
| Deterministic enforceable rule | scripts / CI |

A machine-checkable rule belongs in code/CI when practical. Subjective product quality requires structured human/design/usability evidence rather than pretending a validator can decide whether an interface is beautiful or intuitive.

---

# 9. Quick reference

## New UI repository

```text
select stack + product-ui
 -> specialize baseline + design contracts
 -> map commands
 -> define critical journeys/states/accessibility
 -> implement product
 -> validate engineering + experience
```

## Existing UI repository

```text
audit KEEP/ADAPT/ADD/N/A/CONFLICT
 -> preserve design/native strengths
 -> adopt product-ui semantics
 -> close real UX gaps
 -> validate critical journeys/accessibility/layout
```

## Headless repository

```text
select applicable stack profiles
 -> product-ui N/A
 -> product-experience validator PASS (not applicable)
```

The intended outcome is simple: use `repo-template-sw` to establish **how the software is engineered, operated and—when a UI exists—understood by the user**, then let each project remain self-contained with native implementation choices.
