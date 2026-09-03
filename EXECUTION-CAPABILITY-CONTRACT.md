# Validation Execution Capability Contract

Version: 0.3.1

This contract defines **when validation runs, how much evidence is justified, who executes it, and when existing proof may be reused**. It complements `STANDARD.md`, `OPERATING-CONTRACT.md` and `E2E-ENVIRONMENT-CONTRACT.md` without weakening final confidence.

## Governing rules

> Optimize for sufficient confidence per unit of feedback time, not maximum validation on every edit.

> Delivery stage and validation depth are independent.

> Select concrete risk gates first; `LEAN`, `SCOPED`, `STRONG`, `FULL` are shorthand, not monolithic suites.

> Automation executes automatable work; lack of local tooling does not make the user the test runner.

## 1. Delivery stages

### `ITERATION`

Fast falsification while implementing a coherent slice. Prefer touched formatting/static checks, affected compile and focused unit/contract tests. Exact-head publication evidence, full-diff review, durable-doc freshness, remote preflight and E2E are not defaults. Draft collaboration may exist without being integration-ready.

Template feedback target: about three minutes where practical.

### `INTEGRATION`

A coherent observable slice is ready to converge. Refresh base/head identity, review the complete diff, make affected durable docs current, resolve risk dimensions and required gates, execute/rout deterministic evidence, and run only affected critical journeys that lower-level tests cannot prove.

Exact-head evidence is required for the integration candidate. Template feedback target: about eight minutes where practical.

### `RELEASE`

Promotion/reference-grade checkpoint. `FULL` validation is normal, release-critical package/E2E gates run, exact candidate identity and docs are current, and residual real-environment evidence stays explicit.

## 2. Execution classes

Every required gate is one of:

- `AGENT_LOCAL` — executable directly by the current coding agent;
- `REMOTE_AUTOMATED` — deterministic/automatable but unavailable locally, so repository automation owns execution;
- `REAL_ENVIRONMENT` — genuinely requires representative hardware, protected authority, external environment or human judgement automation cannot truthfully replace.

Ordinary compile, lint, unit, R8, unsigned build and emulator work is `REMOTE_AUTOMATED`, not `REAL_ENVIRONMENT`, when a coding agent lacks the toolchain.

## 3. Risk dimensions and validation depth

Selectors resolve **risk dimensions -> required gates -> profile shorthand**. Typical risks include executable owner/module, public API/protocol, persistence/migration, privacy/security, runtime/resource/lifecycle, native/JNI, dependency/manifest/package/R8, UI/accessibility/adaptive behavior, critical journeys and validation/build machinery.

- `LEAN` — docs/governance/metadata or cheap universal guards.
- `SCOPED` — contained owner/module plus direct consumers and focused checks.
- `STRONG` — cross-boundary or release-sensitive behavior such as public contracts, persistence/security, native/JNI, packaging/R8/manifest/dependency or lifecycle ownership.
- `FULL` — release/promotion, selector/global-build/toolchain changes, unknown executable scope or explicit full request.

Unknown executable scope fails safe stronger. Selector/global validation machinery changes force `FULL`. Stronger explicit validation is allowed; silent downgrade below `auto` is not.

## 4. No-human-runner principle

When a deterministic gate is required but unavailable locally:

```text
resolve required gate
-> classify REMOTE_AUTOMATED
-> reuse valid evidence if equivalent
-> otherwise trigger repository automation
-> inspect/fix owning cause
-> rerun only invalidated evidence
```

Do not ask the user to run the same automatable command merely because the agent lacks a shell/SDK.

## 5. Evidence identity and reuse

Before starting expensive automation, search for successful evidence that still proves the required claim.

Normal integration evidence identity includes:

- exact source head;
- source Git tree when available;
- intended target/base relationship;
- required gates;
- selected profile or stronger equivalent profile;
- relevant E2E environment/fidelity/evidence mode.

PR number, labels, comments and draft/ready state are collaboration metadata, not proof identity.

### Exact-head reuse

Use when head, material base, gates/profile and relevant E2E identity still match. Recreating a PR or changing collaboration metadata alone does not invalidate evidence.

### Content-preserving post-merge reuse

After a squash/rebase or other content-preserving integration transformation, a repository may reuse successful **integration** evidence even though the commit SHA changed, but only when:

1. the post-merge commit Git tree exactly matches the validated candidate tree;
2. the push base exactly matches the target/base revision used by that validation;
3. required gates/profile and relevant E2E identity are equal or weaker than the validated proof;
4. the evidence was produced by trusted repository-owned automation and is current.

This proves content equivalence, not that the previous run executed on the new commit object. Report the reused source run/identity truthfully.

If the base advanced before merge, the tree differs, gates broaden, evidence expired, or identity cannot be established, **do not reuse**. A direct integration-branch push without matching trusted evidence validates normally.

`RELEASE` remains exact-candidate/reference-grade unless the repository explicitly defines a stronger release-specific equivalence rule.

## 6. Remote preflight

Repositories maintained by execution-limited agents should expose an agent-triggerable remote path. Resolve required gates, reuse valid evidence, then execute only missing/stale/insufficient gates. New runs remain exact-head pinned, least-privilege, secret-safe and bounded.

Readiness states:

- `READY_FOR_CI` — required deterministic integration gates available locally passed;
- `READY_FOR_REMOTE_PREFLIGHT` — semantic/base/diff/docs checks passed but remote deterministic gates remain;
- `AUTOMATED_PREFLIGHT_CONFIRMED` — all required deterministic gates are satisfied by current valid evidence, reused or newly executed;
- `NOT_READY_FOR_AUTOMATED_PREFLIGHT` — ambiguity, stale base/docs/diff, failed/missing evidence or unsafe scope prevents readiness.

Real-environment evidence is tracked separately and still blocks claims that depend on it.

## 7. Failure loop

```text
failure
-> inspect evidence
-> classify cause
-> identify invariant + owner
-> patch owning cause
-> re-evaluate risks/gates
-> invalidate only affected evidence
-> reuse/rerun what remains
```

Do not weaken legitimate gates or repeatedly patch symptoms without a new falsifiable hypothesis.

## 8. Validation economics

Where practical observe duration, flake rate, unique regression signal and overlap. Move high-signal cheap checks earlier and expensive low-frequency checks to the checkpoint where they add value. Frequent `FULL` on contained work is selector/design feedback; repeated misses by narrow gates mean the mapping should strengthen.

The goal is not fewer tests. It is **the cheapest feedback loop that preserves sufficient confidence at the current delivery stage**.

## 9. Capability gaps

If required deterministic work is automatable but unavailable locally and remotely, report `AUTOMATION_CAPABILITY_GAP`. If affected risks/gates cannot be determined safely, report `VALIDATION_SCOPE_GAP` and fail safe stronger while improving the selector.
