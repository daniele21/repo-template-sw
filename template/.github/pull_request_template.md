## What changed

<!-- Small, concrete summary. -->

## Why

<!-- Problem/outcome and important tradeoffs. -->

## Invariants / risk

<!-- Public contracts, data, resource, failure, security, migration or operating-lifecycle implications. Write N/A when truly not applicable. -->

## Product experience

<!-- If product-ui/user-facing behavior is affected: task/IA/progressive disclosure, critical states/feedback/recovery, accessibility/adaptive layout, design-system/brand implications and critical journeys. Otherwise N/A. -->

## Build / runtime / artifact lifecycle

<!-- If applicable: canonical command intents affected; build identity; artifact manifest/checksum/build delta/retention; localhost/process/port/temp cleanup. Otherwise N/A. -->

## Pre-publication readiness

<!-- Exact HEAD and intended target/base revision; material ambiguity resolved; full diff reviewed; target-base freshness. State READY_FOR_CI, READY_FOR_REMOTE_PREFLIGHT, AUTOMATED_PREFLIGHT_CONFIRMED or blocked reason truthfully. -->

## Validation profile

<!-- AUTO resolution: LEAN / SCOPED / STRONG / FULL, why it was selected, and affected modules/components/jobs. Stronger manual override is fine; weaker-than-auto requires explicit justification. -->

## Agent-local validation

<!-- Required gates in the selected profile that the current coding agent could execute directly. Use PASS/FAIL/N/A. -->

## Remote automated validation

<!-- Deterministic automatable gates in the selected profile that were unavailable to the current agent locally. Record trigger/run identity and PASS/FAIL/PENDING/N/A. Do not delegate these to the user merely because the agent lacks an execution environment. -->

## E2E environment / fidelity evidence

<!-- For each affected critical journey: .engineering/e2e.json journey id, execution-environment id, fidelity class, built/package surface used, PASS/FAIL/PENDING/N/A, and residual target-environment gaps. Do not promote emulator/simulator evidence into physical/target evidence. Otherwise N/A. -->

## Real-environment evidence

<!-- Physical-device/hardware/protected external/manual/usability evidence that automation cannot truthfully replace. Tie it to residual E2E fidelity gaps when applicable. State PASS/PENDING/N/A and why. -->

## Product-experience evidence

<!-- If a stable high-risk UI surface was affected: accessibility/visual/usability evidence and relevant critical-journey evidence. Otherwise N/A. -->

## Evidence lifecycle

<!-- Cleanup verification plus trace/screenshot/video/log identity, privacy and bounded-retention policy when applicable. -->

## Documentation / design lifecycle

<!-- Durable docs/design/E2E contracts updated, or why none are required. Completed workstream deleted/finalized when applicable. Generated screenshots are evidence, not default durable design truth. -->
