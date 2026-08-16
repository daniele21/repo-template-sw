# <PROJECT_NAME> — Coding Agent Guide

This file is the repository-wide navigation layer for coding agents. It owns durable invariants, routing and validation selection. It is not a project-status ledger or a substitute for architecture/feature documentation.

## Read only what the task requires

Always read this guide. Then read only:

1. the closest scoped `AGENTS.md`, when one exists for the target subtree;
2. the canonical architecture/feature/workstream source required by the task;
3. the owning implementation, direct consumers and nearby tests.

Do not load every plan or all documentation for a local change.

## Repository purpose

<REPLACE_WITH_3_TO_6_LINES_DESCRIBING_THE_PRODUCT_AND_PRIMARY_RUNTIME>

## Non-negotiable invariants

<REPLACE_WITH_PROJECT_SPECIFIC_DURABLE_INVARIANTS>

Keep this list short. Do not copy generic advice already enforced by `STANDARD.md`, Skills or CI unless the project needs a local specialization.

## Ownership and routing

| Change | Start here | Inspect next |
| --- | --- | --- |
| <public/domain contract> | <owner path> | <direct consumers/tests> |
| <runtime/lifecycle> | <owner path> | <adapters/persistence/tests> |
| <persistence/data lifecycle> | <owner path> | <migrations/consumers/tests> |
| <UI/transport adapter> | <owner path> | <owning domain contract/tests> |

Add scoped `AGENTS.md` files only for subtrees with meaningful local invariants, hazards, ownership or validation commands.

## Core change workflow

1. Confirm the owning boundary and smallest coherent scope.
2. Use `plan-workstream` only for work large enough to need dependency/state coordination.
3. Use `structured-change` before and after meaningful code changes.
4. Inspect owner, direct consumers, fakes and tests before changing a shared contract.
5. Implement one coherent vertical slice without speculative layers.
6. Use `validate-change` to choose the narrowest sufficient validation while iterating, then expand according to blast radius.
7. Update only the canonical durable document whose current behavior/decision changed.
8. When an active workstream completes, use `finalize-workstream` to transfer durable knowledge and delete the plan by default.
9. Inspect the complete diff before publishing.

## Validation routing

<REPLACE_WITH_PROJECT_SPECIFIC_TARGETED_AND_FULL_VALIDATION_COMMANDS>

A missing real-device/hardware run must be reported as pending; never promote emulator/synthetic evidence into a stronger claim.

## Documentation lifecycle

- `docs/architecture.md` owns current architecture/ownership.
- `docs/features/` owns durable feature behavior when additional documentation is needed.
- `docs/adr/` owns accepted durable architectural decisions.
- `docs/current-state.md` is the single short repository-level operational ledger.
- `docs/workstreams/` contains only active bounded implementation plans.
- Completed plans are deleted after durable behavior/decisions are transferred. Archive only with independent audit/regulatory/release/historical justification.
- Git history owns implementation history.

Do not create plan/progress/status documents that duplicate the same workstream.

## Agent context discipline

Prefer scoped search (`rg`, targeted file reads, symbol/caller discovery) over broad file ingestion. Do not read generated outputs, dependency trees, vendored code or large artifacts unless the task requires them.

Keep this guide within the configured budget in `.engineering/documentation-policy.json`. Put conditional procedures in Skills and deterministic rules in scripts/CI rather than growing this file.

## Stop conditions

Surface the conflict instead of improvising when a requested change would violate a durable invariant/accepted ADR, expose secret/private state, create a second source of truth, bypass required review for destructive/migrating behavior, or claim evidence that was not executed.
