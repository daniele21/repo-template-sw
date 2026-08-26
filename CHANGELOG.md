# Changelog

## 0.6.0 — 2026-08-26

Moves coding-agent quality assurance decisively before remote CI and makes delivery readiness a first-class engineering contract:

- introduces the rule **CI should confirm, not discover** for deterministic repository failures that can be reproduced locally;
- adds a machine-readable `publication_gate` to `.engineering/commands.json` without forcing a universal wrapper command or replacing stack-native tooling;
- adds the core `preflight-change` Skill, which establishes `READY_FOR_CI` only after material ambiguity is resolved, the intended target base is fresh, the complete diff is reviewed, and every required locally reproducible deterministic gate passes on the exact head;
- distinguishes iteration validation (`validate-change`) from final publication readiness (`preflight-change`);
- adds an explicit material-ambiguity protocol: inspect canonical repository evidence first, then ask the user when unresolved alternatives would materially change behavior, contracts, persistence, security, lifecycle, compatibility, acceptance criteria or UX;
- adds a failure root-cause protocol that classifies failures before modifying production code and prohibits test suppression or repeated symptom patching without a new falsifiable hypothesis;
- makes stacked/base-dependent work conditional until dependencies land and exact-head/base validation is refreshed;
- requires deterministic local/CI parity where practical so GitHub Actions invokes the same project-owned validation semantics used by developers/agents;
- strengthens PR readiness reporting with PASS/FAIL/PENDING/N/A evidence and explicit CI-only/device/hardware pending gates;
- extends Android guidance so cheap format/lint/compile/unit gates are expected before publication rather than being delegated to Actions;
- adds L2 feedback through CI first-pass health so recurring avoidable failures are systematically moved earlier into preflight.

The delivery model is now: **reason first -> validate locally -> prove exact-head readiness -> CI confirms -> stronger real-environment evidence completes the claim.**

## 0.5.0 — 2026-08-23

Makes product-experience reasoning an explicit, ordered and proportional workflow instead of a flat collection of UX/UI requirements:

- `PRODUCT-EXPERIENCE-CONTRACT.md` now defines the default decision order: user outcome -> task model -> information architecture/critical journey -> information/action hierarchy -> progressive disclosure/defaults -> interactions/states/feedback/recovery -> adaptive/platform behavior -> accessibility -> design system/components -> motion -> visual polish/graphics -> validation;
- new core `design-product-experience` Skill routes meaningful structural UX, interaction and motion/visual-system changes through that order while keeping visual-only edits proportional;
- `template/AGENTS.md`, `structured-change` and `validate-change` now route meaningful `product-ui` work through the new Skill and explicitly prohibit using motion/graphics/polish to compensate for unresolved structure;
- `design/ux-contract.json` now records primary users/jobs/surfaces, decision-model invariants, purposeful motion semantics and functional-before-decorative graphics roles;
- `design/brand-kit.json` now separates product-owned motion language/tokens (durations, easing, spring/bounce, reduced-motion strategy) from universal UX motion semantics;
- motion is standardized by purpose (feedback, continuity, spatial relationship, state transition, progress, attention, hierarchy, meaningful completion) without forcing universal timings/easings;
- graphics/imagery are treated as functional product tools before decoration, and functional UI must remain understandable without decorative imagery;
- `verify_product_experience.py` validates the new machine-readable decision, motion, graphics and brand-motion contract fields while still avoiding false claims that CI can judge beauty/usability;
- `verify_repository.py` and baseline Skill metadata include `design-product-experience`;
- `product-ui`, adoption and update guidance now preserve proportional design depth and provide an explicit 0.4.x -> 0.5.x migration path.

The central product-experience rule is now: **UX before UI. Interaction before motion. Structure before polish. Evidence before completion.**

## 0.4.0 — 2026-08-17

Adds an optional, stack-neutral Product Experience Contract for repositories with a material user interface:

- `PRODUCT-EXPERIENCE-CONTRACT.md` covering information architecture, progressive disclosure, cognitive load, sensible defaults, action hierarchy, complete UI states, feedback, error recovery, accessibility, adaptive layout, brand/design-system ownership, critical journeys and UX evidence;
- optional `product-ui` profile that preserves platform-native interaction/tooling rather than imposing one visual framework;
- `design/ux-contract.json` and `design/brand-kit.json` template contracts;
- zero-dependency `verify_product_experience.py`, enforced in template CI and automatically `N/A` for adopted repositories without `product-ui`;
- canonical design source-of-truth and key-reference-view requirements instead of uncontrolled mockup/screenshot revisions;
- semantic brand/design tokens instead of scattered raw visual values;
- progressive disclosure and intentional hierarchy as requirements for primary interfaces;
- loading/empty/error/disabled states and actionable recovery treated as correctness concerns;
- WCAG 2.2 AA or stronger declared target for web, with equivalent platform accessibility semantics for native apps;
- responsive/adaptive behavior that preserves content priority;
- critical user journeys linked to E2E where lower-level tests cannot prove the full outcome;
- visual/accessibility/E2E evidence following bounded retention, identity and zero-residue cleanup;
- L0/L1/L2 product-UI maturity for progressively stronger UX, accessibility, regression and usability evidence.

The experience model follows **same semantics, native implementation**: projects converge on clarity, accessibility, progressive disclosure and recoverability without being forced into identical visuals, component libraries or design tools.

## 0.3.0 — 2026-08-17

Adds end-to-end validation as a first-class but stack-neutral part of the project operating contract:

- new canonical `e2e` command intent in `.engineering/commands.json`;
- E2E is recommended rather than universally mandatory, and may be `n/a` only when no meaningful whole-system/user journey exists;
- L1 expects automated end-to-end evidence for critical workflows when lower-level tests cannot establish the full outcome;
- L2 expects stronger coverage of critical journeys, representative failure/recovery paths and real artifact/device execution where applicable;
- E2E is explicitly distinct from `smoke`: smoke proves minimal runtime/artifact viability, E2E proves a complete workflow outcome;
- E2E runs inherit the zero-residue contract for processes, listeners, browser/device sessions, downloads, test data, temporary workspaces, logs, screenshots, traces and videos;
- failure evidence such as traces/screenshots/logs is treated as bounded CI artifact evidence with build/run identity;
- TypeScript/web guidance prefers Playwright for browser E2E unless an equally strong established solution already exists;
- Android guidance maps E2E to Compose UI Test/Espresso/UI Automator or the established native equivalent;
- macOS guidance maps E2E to XCTest/XCUITest or the established native equivalent;
- Python/server guidance maps E2E to real-process/API workflows rather than introducing browser tooling where no browser exists;
- validation Skills and agent routing now use the canonical `e2e` intent when the blast radius crosses a complete workflow boundary.

This remains **same semantics, native implementation**: the baseline requires the evidence boundary, not one universal E2E framework.

## 0.2.0 — 2026-08-16

Adds a common, stack-neutral project operating contract while preserving native tooling per repository:

- canonical command intents for `setup`, `doctor`, `dev`, `check`, `test`, `build`, `smoke`, `package`, `stop` and `clean`;
- machine-readable `.engineering/commands.json` contract;
- zero-dependency operating-contract validation in project and template CI;
- unique build identity and artifact-lineage semantics;
- immutable successful artifacts with staging/promote behavior, manifests and SHA-256 checksums;
- default local retention of the latest two successful builds per lineage;
- temporary CI-artifact vs durable release-artifact storage policy;
- generated `BUILD_CHANGELOG.md` delta for every successful comparable build;
- localhost/runtime ownership rules: loopback default, collision-aware ports, graceful shutdown and no residual project-owned listeners;
- zero-residue lifecycle rules for processes, sockets, locks, temp data, test databases, logs, caches and other ephemeral resources;
- repeatability and post-clean verification as reference-grade expectations;
- Android, macOS, Python, TypeScript and local-AI profile guidance aligned to the common command/lifecycle semantics.

This is a semantic baseline migration: adopted repositories must classify and implement the relevant operating-contract deltas rather than only bumping metadata.

## 0.1.0 — 2026-08-16

Initial agent-native reference engineering baseline:

- universal L0/L1/L2 engineering standard;
- project-local agent operating model;
- disposable workstream planning lifecycle;
- core reusable coding-agent skills;
- token and documentation budgets;
- zero-dependency repository health checks;
- adoption and update workflows;
- optional stack/domain profile model.
