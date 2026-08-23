---
name: update-engineering-standard
description: Migrate an already-adopted repository from its recorded repo-template-sw baseline to a newer version through an explicit semantic delta review that preserves local engineering and product-experience customizations and applies only relevant changes.
---

# Update Engineering Standard

## Principle

An adopted repository is self-contained. Standard updates are explicit migrations, not automatic synchronization.

## Workflow

1. Read the project's `.engineering/baseline.json`, `.engineering/commands.json` and current local Skills/guides; read `design/ux-contract.json` and `design/brand-kit.json` when `product-ui` is adopted.
2. Read `VERSION`, `CHANGELOG.md`, `STANDARD.md` and changed focused contracts in `repo-template-sw` from recorded version to target version.
3. Classify each delta:
   - `APPLY` — directly relevant and local copy is unmodified;
   - `MERGE` — relevant but local file/Skill/operating/design mechanism is customized;
   - `N/A` — profile/concern not used by the project;
   - `DEFER` — valid change intentionally postponed with a named reason/owner;
   - `CONFLICT` — requires an explicit architecture/product decision.
4. Inspect local behavior before replacing text. Never overwrite a customized Skill, project-specific `AGENTS.md`, native build tooling, E2E framework, design system/source of truth or release flow wholesale.
5. Map common semantics onto existing native mechanisms before adding wrappers/frameworks. Preserve stronger local build/runtime/artifact/E2E/design mechanisms.
6. Implement the smallest migration that establishes the new invariant/behavior.
7. Run baseline health checks plus project-specific validation affected by the migration. Run applicable E2E for full workflows, product-experience evidence for UI changes, and build/smoke/stop for build/runtime lifecycle changes.
8. Update `.engineering/baseline.json` source version and per-Skill `source_version`; preserve `customized: true` where local divergence remains intentional.
9. Update `.engineering/commands.json` or design contract versions/mappings only after corresponding behavior is real.
10. Update durable project docs/design contracts only when current behavior/ownership changed.
11. If a migration workstream was required, finalize and delete it by default.

## 0.2 operating-contract migration guidance

When migrating from 0.1.x to 0.2.x, explicitly classify canonical commands, build identity, artifact lineage/manifest/checksum/retention/release storage, generated build delta, localhost/process/port ownership, ephemeral cleanup and operating-contract CI enforcement.

Do not claim 0.2 adoption by only copying `.engineering/commands.json` or bumping metadata.

## 0.3 E2E migration guidance

When migrating from 0.2.x to 0.3.x, explicitly classify:

- whether critical complete workflows need E2E evidence;
- canonical `e2e` command mapping or truthful `n/a`;
- existing E2E framework/tooling that should be `KEEP`;
- a small critical-journey set instead of broad brittle UI automation;
- built/package artifact execution when the claim depends on it;
- E2E cleanup and bounded identity-bearing failure evidence;
- CI cadence appropriate to E2E cost.

For browser/web projects adding a new E2E framework, prefer Playwright unless an equally strong established solution already exists.

## 0.4 product-experience migration guidance

When migrating from 0.3.x to 0.4.x, first decide whether a material user-facing interface exists.

If not, classify `product-ui` as `N/A`; `verify_product_experience.py` should pass as not applicable.

If yes, explicitly classify:

- whether to adopt `product-ui` in `.engineering/baseline.json`;
- canonical design source of truth (Figma, code-first, in-repo or other real owner);
- existing brand kit/tokens/components that should be `KEEP` rather than duplicated;
- user task model and information architecture;
- primary action hierarchy, progressive disclosure and sensible defaults;
- critical loading/empty/error/disabled and other reachable states;
- feedback/progress/error recovery behavior;
- accessibility target and real automated/manual evidence;
- responsive/adaptive contexts;
- critical user journeys and linkage to existing/new E2E where lower-level tests are insufficient;
- key reference views without screenshot/mockup sprawl;
- visual regression for stable high-risk surfaces where useful;
- usability evidence for high-risk/important flows where justified;
- bounded/identity-bearing UI test evidence and zero-residue cleanup.

Do not create a second design system, copy Figma into static screenshots, or introduce a UI framework merely to satisfy the baseline. `design/ux-contract.json` and `design/brand-kit.json` may route to existing stronger owners.

A metadata-only 0.4 bump, or selecting `product-ui` while leaving generic placeholders, is not a valid migration.

## 0.5 product-experience workflow migration guidance

When migrating from 0.4.x to 0.5.x, the main change is not a new visual style. It is an explicit decision workflow that keeps structural UX ahead of implementation/polish and separates UX motion semantics from project-specific motion language.

For repositories without `product-ui`, keep the new `design-product-experience` Skill as a dormant core Skill unless local policy intentionally removes non-applicable Skills and records that customization.

For repositories with `product-ui`, explicitly classify and merge:

- `design-product-experience` Skill routing in `AGENTS.md`;
- primary users/jobs/surfaces in `design/ux-contract.json`;
- decision-model invariants: user outcome first, task model before layout, hierarchy before polish, states before motion, purposeful motion and evidence before completion;
- proportional change depth: structural UX vs interaction vs visual-only;
- motion semantics/purposes in the UX contract;
- functional-before-decorative graphics/imagery semantics;
- project-owned motion character/tokens in `design/brand-kit.json` (durations, easing, spring/bounce and reduced-motion strategy) without replacing stronger existing design-system tokens;
- verifier changes for the new machine-readable fields;
- `structured-change` and `validate-change` routing so meaningful UX/UI work cannot silently jump straight to components, animation or graphics.

Preserve stronger existing product/design processes. If an established design system already owns motion tokens, point/map `brand-kit.json` to that truth rather than introducing duplicate values.

Do not treat migration as permission to redesign the product. The semantic migration is complete when future meaningful UX/UI work is routed through the ordered, proportional process and the machine-readable project contracts reflect real current ownership/semantics.

A metadata-only 0.5 bump is not a valid migration.

## Output

Report:

- old -> new baseline version;
- deltas applied/merged/deferred/not applicable;
- local customizations preserved;
- operating/E2E/product-experience mappings and migrations;
- validation/evidence executed;
- unresolved conflicts/deferred migrations.

A version bump without applying or explicitly classifying relevant semantic deltas is not a valid migration.
