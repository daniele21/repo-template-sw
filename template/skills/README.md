# Core project-local Skills

These Skills are copied into adopting repositories and versioned with the project. They encode recurring procedures that should not inflate the root `AGENTS.md`.

Core set:

- `plan-workstream` — create a bounded dependency-aware active plan only when coordination is justified;
- `structured-change` — preserve ownership, simplicity, ambiguity, resource/failure/data invariants during meaningful changes;
- `design-product-experience` — reason through meaningful UX/UI work in the correct order, with proportional depth, before implementation/polish;
- `validate-change` — choose the narrowest sufficient validation while iterating and diagnose failures at their owning invariant;
- `preflight-change` — establish exact-head/base `READY_FOR_CI` only after material ambiguity, full-diff review and all locally reproducible deterministic gates are resolved;
- `finalize-workstream` — transfer durable knowledge and delete completed plans by default;
- `review-reference-quality` — perform an L0/L1/L2 gap review before important milestones.

Projects may specialize local copies. Record customization in `.engineering/baseline.json` so future baseline migrations merge rather than overwrite local procedure.

`design-product-experience` is conditional in use: headless repositories keep the Skill dormant, while repositories adopting `product-ui` use it for meaningful structural UX, interaction or motion/visual-system changes. Local visual-only token/style edits should stay proportional rather than expanding into unnecessary design process.

`preflight-change` is a publication boundary, not a replacement for the fast edit-test loop. Use `validate-change` while iterating, then use preflight once the change is believed complete and again after any head/base change that invalidates evidence.

Do not create a Skill for one-off instructions. A Skill is justified when a procedure recurs, is conditional, has non-obvious ordering/hazards, or saves substantial repeated agent context.
