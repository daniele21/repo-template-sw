# repo-template-sw — Coding Agent Guide

This repository owns the reusable engineering baseline distributed to other projects. Keep it small, stack-neutral at the core, and safe to copy.

## Read only what the task requires

- `STANDARD.md` owns universal engineering invariants and maturity levels.
- `OPERATING-CONTRACT.md` owns universal setup/dev/test/E2E/build/smoke/package/artifact/runtime/cleanup semantics.
- `E2E-ENVIRONMENT-CONTRACT.md` owns universal E2E target-environment/fidelity semantics and the rule that final target validation confirms residual environment-specific claims rather than becoming the first complete-system test.
- `EXECUTION-CAPABILITY-CONTRACT.md` owns universal validation-executor classification, remote-preflight and no-human-runner semantics.
- `PRODUCT-EXPERIENCE-CONTRACT.md` owns universal UX/UI semantics for repositories that opt into `product-ui`.
- `template/` owns files copied/specialized into projects.
- `template/.engineering/commands.json` owns the machine-readable adopter operating/execution-contract shape/defaults.
- `template/.engineering/e2e.json` owns the machine-readable adopter E2E target environments, execution environments, fidelity gaps and critical-journey mapping.
- `template/design/` owns the opt-in adopter UX/brand contract templates.
- `template/skills/` owns project-local recurring workflows, including `preflight-change` and `remote-preflight`.
- `profiles/` owns optional stack/domain/product-surface additions and native mappings.
- `skills/adopt-engineering-standard/` owns first adoption/alignment.
- `skills/update-engineering-standard/` owns later migrations between baseline versions.
- `CHANGELOG.md` owns version deltas that matter to adopters.

Do not load every profile or Skill when changing one concern.

## Non-negotiable invariants

- The universal core must not depend on a specific language, framework, cloud, design tool, visual trend or application architecture.
- Common command names define semantics, not a universal build tool; do not force wrappers merely for uniformity.
- Required validation strength is independent from the current coding agent's execution environment.
- An automatable deterministic gate must not become a user task solely because the current agent lacks a shell, checkout, SDK or platform toolchain.
- When an equivalent local environment exists, local execution should precede CI confirmation; when it does not, repository-owned remote automation is a valid execution backend.
- E2E execution capability and E2E environment fidelity are independent dimensions; emulator/simulator evidence must not be promoted into physical/target-environment evidence.
- Critical E2E journeys must declare the target environment, automated execution environments and residual fidelity gaps so final real-environment validation primarily confirms what automation cannot faithfully reproduce.
- Critical E2E journeys that traverse a UI must emit identity-bearing, privacy-safe UI screenshot artifacts; a run without the required screenshots is incomplete E2E evidence even when the test assertions themselves passed.
- Remote execution of change-branch code must preserve least privilege and must not gain production/signing/deployment secrets merely for convenience.
- Product-experience semantics define clarity/accessibility/recoverability, not one visual style or component framework.
- `product-ui` is opt-in and only appropriate for repositories with a material user-facing interface.
- A project remains self-contained after adoption; ordinary development must not require this repository at runtime.
- Template updates are explicit migrations, never invisible remote behavior changes.
- Prefer zero-dependency validation tooling when the standard library is sufficient.
- Do not duplicate universal rules across profiles or Skills; link to the canonical owner and add only procedure/specialization.
- `AGENTS.md` is routing/invariants, `.engineering/commands.json` is project operation/execution routing, `.engineering/e2e.json` is E2E environment/fidelity routing, `design/*` is product-experience/brand routing when applicable, Skills are conditional procedures, docs are durable facts, scripts/CI are deterministic enforcement/execution.
- Completed implementation plans are deleted by default after durable knowledge transfer; Git is implementation history.
- Material builds/artifacts/runtimes follow operating-contract identity, bounded-retention and zero-residue invariants without prescribing stack-native implementation details.
- UI products should use a declared design source of truth, semantic tokens, progressive disclosure and appropriate accessibility/critical-journey evidence without creating a second visual truth in generated screenshots.
- Do not add a template file because it is merely common. Add it only when it protects a meaningful cross-project invariant or recurring workflow.
- Profiles add the smallest justified stack/domain/product delta; they do not become internal frameworks.
- Changes to required invariants, copied Skills or machine-readable semantics require a baseline version/changelog decision.

## Change workflow

1. Identify whether the change belongs to universal standard, operating contract, E2E environment contract, execution-capability contract, product-experience contract, copied template, optional profile, adoption/update procedure or validation tooling.
2. Check direct consumers: a copied template change may affect every future adopter; a Skill/contract change may change coding-agent behavior.
3. Prefer a small compatible change. If adopter behavior changes materially, update `VERSION` and `CHANGELOG.md` coherently.
4. Keep template files usable after project-specific specialization; do not bake repository-specific paths, build systems, design tools or visual styles into the universal core.
5. When changing operating/execution semantics, update machine-readable defaults, verifier, applicable Skills/profiles and adoption/update guidance together.
6. When changing E2E environment-fidelity/evidence semantics, update `E2E-ENVIRONMENT-CONTRACT.md`, `.engineering/e2e.json` when its routing shape changes, validation/preflight routing and applicable profile/adoption/update guidance together. Runtime-only evidence requirements such as UI screenshot artifacts must still be reflected in the copied validation/preflight workflow even when the static `.engineering/e2e.json` schema does not change.
7. When changing product-experience semantics, update `PRODUCT-EXPERIENCE-CONTRACT.md`, `product-ui`, design templates, verifier, Skills and adoption/update guidance together.
8. Run template validation before publishing.

## Validation

```bash
python3 template/scripts/verify_repository.py --root template --template-mode
python3 template/scripts/verify_operations.py --root template --template-mode
python3 template/scripts/verify_e2e.py --root template --template-mode
python3 template/scripts/verify_product_experience.py --root template --template-mode
python3 template/scripts/verify_docs.py --root template --template-mode
python3 template/scripts/verify_agent_context.py --root template --template-mode
```

Inspect the full diff and ensure no placeholder intended for adopter specialization leaked into universal claims as if it were already configured.
