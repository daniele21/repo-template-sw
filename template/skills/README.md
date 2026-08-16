# Core project-local Skills

These Skills are copied into adopting repositories and versioned with the project. They encode recurring procedures that should not inflate the root `AGENTS.md`.

Core set:

- `plan-workstream` — create a bounded dependency-aware active plan only when coordination is justified;
- `structured-change` — preserve ownership, simplicity, resource/failure/data invariants during meaningful changes;
- `validate-change` — choose the narrowest sufficient validation while iterating and the correct final gate;
- `finalize-workstream` — transfer durable knowledge and delete completed plans by default;
- `review-reference-quality` — perform an L0/L1/L2 gap review before important milestones.

Projects may specialize local copies. Record customization in `.engineering/baseline.json` so future baseline migrations merge rather than overwrite local procedure.

Do not create a Skill for one-off instructions. A Skill is justified when a procedure recurs, is conditional, has non-obvious ordering/hazards, or saves substantial repeated agent context.
