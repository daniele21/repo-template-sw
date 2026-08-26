---
name: preflight-change
description: Establish exact-head automated-validation readiness by resolving material ambiguity, verifying target-base freshness, reviewing the complete diff, classifying validation by execution capability and running or routing every required deterministic gate without turning the user into a test runner.
---

# Preflight Change

Use this Skill immediately before pushing, opening/updating a PR, or otherwise publishing a change for automated validation. `validate-change` owns the iterative test loop; this Skill owns the publication/readiness decision and execution routing.

Read `EXECUTION-CAPABILITY-CONTRACT.md` when the current agent may lack a shell, checkout, SDK or platform toolchain.

The governing rules are:

> CI should confirm locally reproducible deterministic failures when the agent has equivalent execution capability.

and:

> An automatable deterministic gate must not be delegated to the user merely because the current agent cannot run it locally.

## 1. Resolve material ambiguity

Before claiming readiness, confirm that implementation is not resting on an unresolved material assumption.

First inspect canonical evidence:

- owning contract/state/config/design source;
- architecture/feature docs and accepted ADRs;
- direct consumers, fakes/adapters and nearby tests;
- active workstream acceptance criteria when applicable.

Ask the user only when two reasonable interpretations remain and they would materially change product behavior, public/API/protocol contracts, persisted data/migration semantics, security/trust/privacy boundaries, failure/resource/concurrency/lifecycle behavior, backward compatibility, acceptance criteria or meaningful UX.

Do not ask about local naming/style/implementation choices that preserve observable semantics.

## 2. Verify the intended base

Read the intended target branch/ref again before final validation.

- Record exact target/base revision and feature head revision.
- Verify the feature is based on, reconciled with, or proven merge-compatible with the current target according to repository policy.
- Treat stacked work as conditional while parent PRs/dependencies are not integrated.
- After a base/dependency/head change, invalidate prior affected evidence and rerun it.

Do not reuse green evidence from an obsolete head/base relationship.

## 3. Review the complete diff

Inspect the whole diff against the intended base, not only the last edited files.

Look for:

- accidental/generated/private files or debug/logging residue;
- unrelated edits or hidden scope expansion;
- duplicated ownership/policy or a second source of truth;
- weakened/deleted/suppressed tests or validation;
- stale docs/contracts after behavior changed;
- missed direct consumers/fakes/adapters;
- unbounded resources, missing cleanup or changed failure semantics;
- accidental compatibility/migration/security/UX drift.

A diff review is a semantic review, not only a formatting pass.

## 4. Classify required gates by execution capability

Use `validate-change` and `.engineering/commands.json` to select the narrowest sufficient final matrix for the actual blast radius.

For every required gate, assign the execution class for the **current agent/session**:

- `AGENT_LOCAL` — the agent can execute it directly on the exact head;
- `REMOTE_AUTOMATED` — deterministic and automatable, but unavailable in the current agent environment;
- `REAL_ENVIRONMENT` — genuinely requires representative hardware, protected authority, external environment or manual evidence.

Typical deterministic gates include:

- formatting/formatter check;
- lint/static analysis/typecheck;
- touched module/package compilation;
- focused unit/component tests;
- direct-consumer/contract/integration tests;
- canonical repository `check`/`test`;
- build/package/smoke/E2E where the claim requires them.

Do not classify a Gradle/R8/compiler/unit-test gate as `REAL_ENVIRONMENT` merely because ChatGPT lacks an Android SDK. That is `REMOTE_AUTOMATED`.

## 5. Execute or route deterministic validation

Run every required `AGENT_LOCAL` gate on the exact current head.

If all required deterministic gates are `AGENT_LOCAL` and pass, readiness may be `READY_FOR_CI`: remote CI is an independent confirmation environment.

If one or more required deterministic gates are `REMOTE_AUTOMATED` and all semantic/base/diff plus available local gates pass, readiness is `READY_FOR_REMOTE_PREFLIGHT`. Hand off immediately to `skills/remote-preflight/SKILL.md` and trigger the repository-owned automation.

Do **not** ask the user to run an automatable deterministic command solely because the agent lacks a shell, checkout, SDK or toolchain.

If a required deterministic gate is unavailable both locally and through repository-owned remote automation, status is `NOT_READY_FOR_AUTOMATED_PREFLIGHT` with `AUTOMATION_CAPABILITY_GAP`. Prefer adding/repairing automation.

`REAL_ENVIRONMENT` evidence may remain pending after automated validation, but still blocks any stronger claim that depends on it.

## 6. Diagnose failures before editing

For every failure, classify it before changing production code:

- `CHANGE_REGRESSION` — introduced by this change;
- `BASELINE_FAILURE` — reproducible on the intended target base;
- `ENVIRONMENT` — toolchain/dependency/environment mismatch;
- `FLAKY` — non-deterministic and reproduced as such;
- `BASE_DRIFT` — stale/stacked integration effect;
- `ASSUMPTION` — requirement/design/contract assumption is wrong or unresolved.

Then identify the violated invariant and its owner. Fix the owner and add/strengthen regression evidence at the lowest useful level.

Never delete, suppress, weaken or rewrite a legitimate gate simply to make the branch green unless the owning contract itself is intentionally changed.

If the same gate fails again after an attempted fix, stop symptom patching. Re-examine the cause, owner and assumptions and form a new falsifiable hypothesis before editing again. If that exposes material ambiguity, return to section 1 and ask the user.

## 7. Check command parity

Deterministic automation should invoke the same project-owned canonical commands/scripts regardless of whether execution occurs agent-local or remotely. Workflow YAML may orchestrate environment setup, caching and evidence, but should not secretly own a divergent formatter/test/build policy.

If remote automation finds a deterministic failure that an equivalent agent-local environment should have found, close the parity/preflight-selection gap. If the current agent had **no equivalent local execution capability**, the remote discovery is valid execution, not a process defect.

## 8. Output readiness

Report:

```text
HEAD: <revision>
TARGET: <branch>@<revision>
AMBIGUITY: PASS|FAIL
BASE_FRESHNESS: PASS|FAIL
FULL_DIFF_REVIEW: PASS|FAIL
EXECUTION_CAPABILITY: local|mixed|remote-only
AGENT_LOCAL:
  <gate>: PASS|FAIL|N/A
REMOTE_AUTOMATED:
  <gate>: PASS|FAIL|PENDING|N/A
REAL_ENVIRONMENT:
  <gate>: PASS|PENDING|N/A
READINESS: READY_FOR_CI|READY_FOR_REMOTE_PREFLIGHT|AUTOMATED_PREFLIGHT_CONFIRMED|NOT_READY_FOR_AUTOMATED_PREFLIGHT
```

Readiness meanings:

- `READY_FOR_CI` — all required deterministic gates could run agent-local and passed; CI can confirm independently;
- `READY_FOR_REMOTE_PREFLIGHT` — semantic/base/diff checks and all available local gates passed; required deterministic remote gates must now be triggered by the agent;
- `AUTOMATED_PREFLIGHT_CONFIRMED` — every required deterministic automated gate passed on the exact head/base, regardless of execution location;
- `NOT_READY_FOR_AUTOMATED_PREFLIGHT` — a required gate failed, a material ambiguity/base/diff issue remains, or required automation routing is missing.

Any later edit, rebase/merge/replay, dependency change or material target-base movement invalidates the affected evidence.

A known-red draft may be published only when the user explicitly wants a collaboration/investigation artifact. State the known-red condition clearly; do not represent it as automated readiness.
