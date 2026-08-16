# repo-template-sw — Coding Agent Guide

This repository owns the reusable engineering baseline distributed to other projects. Keep it small, stack-neutral at the core, and safe to copy.

## Read only what the task requires

- `STANDARD.md` owns universal engineering invariants and maturity levels.
- `OPERATING-CONTRACT.md` owns universal setup/dev/test/build/smoke/package/artifact/runtime/cleanup semantics.
- `template/` owns files copied/specialized into projects.
- `template/.engineering/commands.json` owns the machine-readable adopter operating-contract shape/defaults.
- `template/skills/` owns project-local recurring workflows.
- `profiles/` owns optional stack/domain additions and native-tool mappings.
- `skills/adopt-engineering-standard/` owns first adoption/alignment.
- `skills/update-engineering-standard/` owns later migrations between baseline versions.
- `CHANGELOG.md` owns version deltas that matter to adopters.

Do not load every profile or Skill when changing one concern.

## Non-negotiable invariants

- The universal core must not depend on a specific language, framework, cloud or application architecture.
- Common command names define semantics, not a universal build tool; do not force Make/Docker/Python wrappers merely for uniformity.
- A project remains self-contained after adoption; ordinary development must not require this repository at runtime.
- Template updates are explicit migrations, never invisible remote behavior changes.
- Prefer zero-dependency validation tooling when the standard library is sufficient.
- Do not duplicate universal rules across profiles or Skills; link to the canonical owner and add only procedure/specialization.
- `AGENTS.md` is routing/invariants, `.engineering/commands.json` is project operation routing, Skills are conditional procedures, docs are durable facts, scripts/CI are deterministic enforcement.
- Completed implementation plans are deleted by default after durable knowledge transfer; Git is implementation history.
- Material builds/artifacts/runtimes follow the operating-contract identity, bounded-retention and zero-residue invariants without prescribing stack-native implementation details.
- Do not add a template file because it is merely common. Add it only when it protects a meaningful cross-project invariant or recurring workflow.
- Profiles add the smallest justified stack/domain delta; they do not become internal frameworks.
- Changes to required invariants, copied Skills or machine-readable semantics require a baseline version/changelog decision.

## Change workflow

1. Identify whether the change belongs to universal standard, operating contract, copied template, optional profile, adoption/update procedure or validation tooling.
2. Check direct consumers: a copied template change may affect every future adopter; a Skill/command-contract change may change coding-agent behavior.
3. Prefer a small compatible change. If adopter behavior changes materially, update `VERSION` and `CHANGELOG.md` coherently.
4. Keep template files usable after project-specific specialization; do not bake repository-specific paths or build systems into the universal core.
5. When changing operating semantics, update machine-readable defaults, verifier, applicable Skills/profiles and adoption/update guidance together.
6. Run template validation before publishing.

## Validation

```bash
python3 template/scripts/verify_repository.py --root template --template-mode
python3 template/scripts/verify_operations.py --root template --template-mode
python3 template/scripts/verify_docs.py --root template --template-mode
python3 template/scripts/verify_agent_context.py --root template --template-mode
```

Inspect the full diff and ensure no placeholder intended for adopter specialization leaked into universal claims as if it were already configured.
