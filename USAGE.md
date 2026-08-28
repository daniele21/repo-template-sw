# Using `repo-template-sw`

This guide explains how to use `repo-template-sw` with a new repository, an existing repository, and an already-adopted repository that needs a baseline migration.

`repo-template-sw` is a **bootstrap, audit and migration source**. After adoption, ordinary work should be driven by the target repository itself.

The governing model is **same semantics, native implementation**: repositories converge on engineering, E2E-evidence and product-experience invariants without being forced onto the same build system, E2E framework/device provider, design tool or visual style.

## Mental model

```text
repo-template-sw
      |
      +--> STANDARD.md                    engineering maturity/invariants
      +--> OPERATING-CONTRACT.md          commands/E2E/build/artifact/runtime
      +--> E2E-ENVIRONMENT-CONTRACT.md    target environments + E2E fidelity
      +--> EXECUTION-CAPABILITY-CONTRACT  who/where executes validation
      +--> PRODUCT-EXPERIENCE-CONTRACT.md UX/UI quality + decision order when applicable
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
      +--> .engineering/commands.json  canonical operations/executor routing
      +--> .engineering/e2e.json       target/execution environments + fidelity gaps + journeys
      +--> design/*                    UX/brand contract if product-ui
      +--> local Skills                recurring workflows
      +--> active workstream           current coordinated work only
      +--> durable docs                current behavior/decisions
      +--> scripts / CI                deterministic enforcement
```

---

# 1. Common operating and E2E model

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

## Execution capability vs environment fidelity

These are separate axes.

Execution capability answers **who/where can execute the gate now**:

```text
AGENT_LOCAL
REMOTE_AUTOMATED
REAL_ENVIRONMENT
```

Environment fidelity answers **how closely the gate's environment represents the target relevant to the claim**:

```text
host_or_fake
simulated_or_emulated
representative_virtual
representative_physical
target_environment
```

Examples:

```text
Android emulator in GitHub Actions
-> REMOTE_AUTOMATED + simulated_or_emulated

Automated physical device farm
-> REMOTE_AUTOMATED + representative_physical

Final run on the actual supported/OEM device
-> usually REAL_ENVIRONMENT + target_environment
```

A green remote job does not automatically strengthen environment fidelity. Emulator/simulator evidence is never physical-device evidence.

## `.engineering/e2e.json`

When E2E is applicable, specialize the machine-readable contract with:

- E2E applicability and reason;
- material target environments;
- platform/device/browser/runtime/hardware dimensions that matter to the product claim;
- automated execution environments and their fidelity class;
- whether the real built/package artifact is exercised;
- known environment gaps;
- a bounded set of critical journeys;
- the minimum automated fidelity expected for each journey;
- residual real-environment confirmation (`required`, `conditional`, `not_required`);
- an explicit automation-gap reason if a required journey genuinely cannot run automatically.

The desired progression is:

```text
prove invariants cheaply
-> prove the complete critical workflow automatically
-> use the built/package artifact when material
-> increase automated environment fidelity only where required
-> confirm residual target-environment gaps
```

Do **not** execute every rung on every change. Blast radius chooses the validation depth; the claim chooses the required environment fidelity.

The final device/manual/production-like test should mainly find defects caused by the remaining reality delta: physical hardware, OEM behavior, thermals, accelerators, protected external environments or genuinely manual judgement. Broken navigation, persistence, IPC/protocol wiring, install/launch, request/response integration and ordinary restart/recovery should be moved into earlier automated E2E whenever practical.

---

# 2. Product experience model

Use the optional `product-ui` profile when the repository has a material user-facing interface. Do not add it to headless libraries/APIs merely because every project uses the same baseline.

A `product-ui` project specializes:

```text
design/
├── ux-contract.json
├── brand-kit.json
└── README.md
```

and uses `skills/design-product-experience/SKILL.md` for meaningful UX/UI work.

The product-experience decision order is:

```text
USER OUTCOME
-> TASK MODEL
-> INFORMATION ARCHITECTURE / CRITICAL JOURNEY
-> INFORMATION + ACTION HIERARCHY
-> PROGRESSIVE DISCLOSURE / DEFAULTS
-> INTERACTIONS + STATES + FEEDBACK + RECOVERY
-> ADAPTIVE / PLATFORM BEHAVIOR
-> ACCESSIBILITY
-> DESIGN SYSTEM / COMPONENTS
-> MOTION
-> VISUAL POLISH / GRAPHICS
-> VALIDATION
```

Use proportional depth:

- **structural UX** — new screen/navigation/workflow/onboarding/major redesign: use the full sequence;
- **interaction** — start from the owning task/journey and cover affected interaction/state/accessibility/adaptive/component/motion layers;
- **visual-only** — preserve settled semantics and start from the canonical design-system/brand owner.

The contract covers primary users/jobs/surfaces, information architecture, progressive disclosure, action hierarchy, complete states, feedback/recovery, accessibility, adaptive behavior, semantic design tokens, component ownership, purposeful motion, functional graphics, bounded key reference views and appropriate UX regression evidence.

`design/ux-contract.json` owns why/when motion or graphics are appropriate. `design/brand-kit.json` owns product-specific visual/motion language or points to the stronger existing Figma/code/design-system truth.

Web products should target WCAG 2.2 AA or stronger; native products should use equivalent platform accessibility APIs/guidelines. Automated accessibility tests do not replace manual keyboard/screen-reader/device validation where important.

---

# 3. Starting a repository from zero

Recommended request to a coding agent:

```text
Bootstrap <TARGET_REPOSITORY> using the current stable baseline of daniele21/repo-template-sw.
Use adopt-engineering-standard.

Before implementing product features:
1. identify product/runtime, languages, platforms, persistence, network/security, build/distribution, target environments and UI boundaries;
2. select only applicable profiles;
3. adopt and specialize the universal baseline;
4. if there is a material UI, add product-ui and specialize design/ux-contract.json and design/brand-kit.json;
5. create a project-specific AGENTS.md with real ownership/routing;
6. map .engineering/commands.json to native tooling;
7. decide E2E applicability; if applicable, specialize .engineering/e2e.json with critical journeys, target environments, automated environments/fidelity and residual gaps;
8. implement build identity, artifact lifecycle/build delta and zero-residue runtime behavior where applicable;
9. for product-ui, define users/jobs, IA/journeys, hierarchy/disclosure/defaults, critical states, accessibility, adaptive/platform behavior, design-system ownership, motion/graphics semantics and key reference views;
10. configure stack-specific format/lint/test/E2E/build/smoke/UI evidence gates;
11. record baseline version/profiles and Skill metadata;
12. run repository, operating, E2E-fidelity, product-experience, documentation and agent-context health checks;
13. report maturity truthfully.

Do not leave placeholders. Do not add profiles, wrappers, E2E/design frameworks or device providers without a real need.
```

Typical profile combinations:

```text
Python local inference service -> python + local-ai
Android app                  -> android + product-ui
Android local-AI app         -> android + local-ai + product-ui
macOS desktop app            -> macos + product-ui
macOS Python local-AI app    -> python + macos + local-ai + product-ui
TypeScript web app           -> typescript + product-ui
Headless Python API          -> python
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
- current E2E execution environments/device farms/browser grids/emulators;
- final manual/device/production-like validation and what defects are first discovered there;
- if UI exists: users/jobs, IA/journeys, progressive disclosure/defaults/action hierarchy, critical states, accessibility, adaptive behavior, design/brand truth, tokens/components, motion/imagery and UX evidence.

Return:
- current L0/L1/L2 estimate;
- KEEP / ADAPT / ADD / N/A / CONFLICT matrix;
- what should explicitly NOT change;
- highest-value gaps;
- a small dependency-aware adoption DAG;
- safe parallel lanes.
```

Then implement incrementally. Preserve native tooling, strong E2E frameworks/device providers and design systems.

For E2E, map each critical journey as:

```text
claim
-> target environment/material dimensions
-> existing/new automated environment + fidelity
-> built/package artifact requirement
-> known gaps
-> residual target-environment confirmation
```

Review failures historically found during final target/manual testing:

- reproducible in an existing automated environment -> move the regression earlier;
- reproducible with a practical stronger automated environment -> add/strengthen it when value justifies cost;
- genuinely hardware/OEM/thermal/protected/manual -> keep as explicit real-environment evidence.

Examples:

```text
Strong Playwright/XCUITest/Espresso suite
-> KEEP; map environments/journeys into e2e.json

Android emulator E2E
-> KEEP; classify simulated_or_emulated, not physical

Existing physical device farm
-> KEEP; classify representative_physical for the claims it represents

Server leaves port open after tests
-> CONFLICT with zero-residue invariant

Strong existing design system/Figma source
-> KEEP; point ux-contract/brand-kit to it

No meaningful UI
-> product-ui N/A
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
commands.json for operations/executor routing
    ↓
e2e.json when complete workflow/environment fidelity matters
    ↓
ux-contract/brand-kit when UI behavior changes
    ↓
local Skill + owning code/tests
```

`validate-change` chooses the narrowest sufficient iteration evidence. If E2E is relevant it chooses the affected critical journey and cheapest sufficient declared automated environment. `preflight-change` then combines blast-radius selection, E2E fidelity selection and executor classification on the exact head/base.

A normal request can stay small:

```text
Implement memory-aware eviction.
```

or:

```text
Redesign model settings so advanced parameters are progressively disclosed.
```

The repository should already encode how to reason about and validate the work.

For UI changes, do not stop at a screenshot. Validate applicable behavior/states, accessibility, layout contexts, motion/performance/reduced-motion semantics, critical journeys and design-system consistency according to the claim.

Use `plan-workstream` only when coordination/dependencies make it useful. Completed plans are deleted after durable knowledge transfer by default.

---

# 6. Updating an adopted repository

Baseline upgrades are explicit semantic migrations, not automatic file synchronization.

Recommended request:

```text
Migrate <TARGET_REPOSITORY> from its recorded repo-template-sw baseline to <TARGET_VERSION>.
Use update-engineering-standard.

Read baseline.json, commands.json, e2e.json when present, local Skills and design contracts when product-ui is adopted.
Compare VERSION/CHANGELOG and changed focused contracts.
Classify each delta APPLY / MERGE / N/A / DEFER / CONFLICT.
Preserve stronger local mechanisms and customizations.
Implement semantic changes, validate them, then update metadata.
```

## 0.7.x -> 0.8.x

The 0.8 migration is an E2E **environment-fidelity** migration, not an E2E-framework replacement.

Explicitly evaluate:

```text
- add/merge E2E-ENVIRONMENT-CONTRACT semantics;
- specialize .engineering/e2e.json;
- identify material target environments/dimensions;
- classify existing E2E execution environments by fidelity;
- map every critical journey to automated environments and minimum fidelity;
- identify built/package-artifact E2E requirements;
- record residual gaps and target-environment confirmation;
- add verify_e2e.py to repository health;
- update validate-change/preflight-change routing;
- review defects currently found only in final manual/device testing and move reproducible ones earlier;
- preserve stronger existing Espresso/Compose UI Test/UI Automator/XCTest/Playwright/device-farm mechanisms.
```

Do not confuse `REMOTE_AUTOMATED` with high fidelity. A CI emulator remains `simulated_or_emulated`; an automated physical device farm can be `representative_physical`.

A metadata-only `0.8.0` bump, an unspecialized placeholder `e2e.json`, or a process where final manual/device testing remains the undocumented first whole-system run is not a valid migration.

For **0.6.x -> 0.7.x**, adopt execution-capability classes, no-human-runner remote preflight and blast-radius profiles.

For **0.5.x -> 0.6.x**, adopt exact-head pre-publication readiness, material-ambiguity/base/diff/root-cause gates and local/CI parity.

For **0.4.x -> 0.5.x**, adopt the ordered/proportional product-experience workflow and `design-product-experience` routing.

For **0.3.x -> 0.4.x**, first classify/adopt the original `product-ui` contract where a material UI exists.

A metadata-only version bump is never a valid semantic migration.

---

# 7. Using the standard as an audit tool

You can use the baseline without modifying a repository:

```text
Review <TARGET_REPOSITORY> against daniele21/repo-template-sw.
Do not change code.

Assess:
- engineering maturity;
- validation executor coverage and remote-preflight capability;
- critical E2E journeys and environment fidelity;
- whether final target/manual tests discover failures that practical automation should catch earlier;
- build/artifact/runtime cleanup;
- when UI exists: users/jobs/task model/IA, progressive disclosure, states/recovery, accessibility, adaptive behavior, design-system ownership, purposeful motion/graphics and UX evidence.

Estimate L0/L1/L2 and rank the highest-value evidence-backed gaps.
```

---

# 8. What belongs where

| Concern | Canonical owner |
| --- | --- |
| Universal engineering invariant | `STANDARD.md` |
| Command/E2E/build/artifact/runtime semantics | `OPERATING-CONTRACT.md` |
| Universal E2E environment/fidelity semantics | `E2E-ENVIRONMENT-CONTRACT.md` |
| Validation executor semantics | `EXECUTION-CAPABILITY-CONTRACT.md` |
| Universal UX/UI semantics + decision order | `PRODUCT-EXPERIENCE-CONTRACT.md` |
| Optional stack/product mapping | `profiles/` |
| Project operations/executor routing | `.engineering/commands.json` |
| Project E2E environments/fidelity/journeys | `.engineering/e2e.json` |
| Project UX contract / users-jobs / motion semantics | `design/ux-contract.json` |
| Project brand/design/motion tokens | `design/brand-kit.json` |
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

Machine-checkable rules belong in code/CI when practical. Subjective product quality requires structured human/design/usability evidence rather than pretending a validator can decide whether an interface is beautiful or intuitive.

---

# 9. Quick reference

## Android / local-AI example

```text
unit + contract
 -> emulator E2E
 -> built APK E2E on emulator
 -> representative physical device when justified
 -> residual OEM/hardware/memory/thermal target confirmation
```

The exact ladder is project-specific. Do not run every rung mechanically.

## New UI repository

```text
select stack + product-ui
 -> specialize baseline + e2e/design contracts
 -> define critical journeys/states/accessibility
 -> map commands/environments
 -> implement product
 -> validate engineering + experience
```

## Headless repository

```text
select applicable stack profiles
 -> product-ui N/A
 -> decide E2E applicability honestly
 -> specialize e2e.json or mark n/a consistently
```

The intended outcome is simple: use `repo-template-sw` to establish **how the software is engineered, validated in environments that progressively represent reality, operated and—when a UI exists—understood by the user**, then let each project remain self-contained with native implementation choices.
