# Validation Execution Capability Contract

Version: 0.3.0

This contract defines **when validation runs, how much validation is justified, and who executes it** when a repository is maintained by humans and coding agents. It complements `STANDARD.md`, `OPERATING-CONTRACT.md` and `E2E-ENVIRONMENT-CONTRACT.md` without weakening final confidence.

The governing rules are:

> Optimize for sufficient confidence per unit of feedback time, not maximum validation on every edit.

> Delivery stage and validation depth are separate dimensions.

> Automation should execute automatable work. A human must not become the fallback test runner merely because a coding agent lacks a local shell, checkout, SDK or build environment.

> Validation selects required risk gates first; `LEAN`, `SCOPED`, `STRONG` and `FULL` are useful shorthand, not a mandate to execute a monolithic suite.

## 1. Delivery stages

Every meaningful change operates in one of three stages.

### `ITERATION`

Use while implementing a coherent slice or subtask.

The goal is rapid falsification of the current edit:

- run the cheapest formatter/static/compile/unit/contract gates that can catch the likely defect;
- do not require exact-head publication evidence, a complete diff review, durable-documentation freshness or remote preflight merely because a branch exists;
- do not run E2E unless the current hypothesis genuinely crosses a complete workflow boundary;
- a draft/collaboration PR may exist without being represented as integration-ready.

Repositories should target a short feedback loop; the template default target is approximately three minutes where the stack permits it. This is a budget signal, not a hard timeout.

### `INTEGRATION`

Use when a vertical slice is ready to converge into the shared integration branch or be marked ready for review/merge.

The goal is to prove the **observable slice outcome** and the material dependency cone:

- refresh intended base/head identity;
- inspect the complete diff;
- make affected durable documentation current;
- select risk dimensions and required gates;
- execute/rout required deterministic validation;
- run the smallest affected critical E2E journey when lower-level evidence cannot prove the outcome;
- require exact-head evidence for the integration candidate.

The template default feedback target is approximately eight minutes where practical. Stronger or slower gates remain justified when the risk demands them.

### `RELEASE`

Use for promotion, release candidates, stable-branch publication or equivalent reference-grade checkpoints.

The goal is full release confidence:

- `FULL` validation is expected;
- release-critical journeys and required artifact/package gates run;
- exact-head/base identity and durable documentation are current;
- residual real-environment evidence remains explicit;
- release/package/signing/promotion semantics apply.

`FULL` is therefore normal at release and exceptional during ordinary feature iteration.

## 2. Execution classes

Every required validation gate is assigned, for the current agent/session, to exactly one execution class.

### `AGENT_LOCAL`

The current coding agent can execute the gate directly in its supported working environment.

Examples include formatter/static checks, focused unit tests, module compilation and repository verifier scripts.

When an equivalent local environment exists, use it before remote confirmation because it provides faster feedback.

### `REMOTE_AUTOMATED`

The gate is deterministic and automatable, but the current agent lacks the required checkout, shell, SDK, toolchain, service or compute capability.

Examples include Android Gradle/R8 validation from a connector-only session or platform-specific builds unavailable in the agent sandbox.

These gates must be delegated to repository-owned automation, not to the user.

### `REAL_ENVIRONMENT`

The evidence genuinely depends on representative hardware, an external protected environment, privileged authority or human judgement that automation cannot truthfully replace.

Examples include physical-device thermals, real hardware/driver behavior, representative usability evidence or owner-only signing approval.

`REAL_ENVIRONMENT` must never become a bucket for ordinary deterministic work that is merely inconvenient for the current agent.

## 3. Risk dimensions and validation depth

Execution location and validation depth are independent from delivery stage.

The repository selector should first identify **risk dimensions** and **required gates**, then summarize the result with a validation profile.

Typical risk dimensions include:

- executable owner/module;
- public/shared API or protocol;
- persistence/migration;
- privacy/security/trust boundary;
- runtime/resource/concurrency/lifecycle;
- native/JNI/backend;
- dependency/manifest/variant/package/R8;
- UI semantics/accessibility/adaptive behavior;
- complete critical journey;
- CI selector/global build/toolchain machinery.

The selector output should be closer to:

```text
risks: ui_behavior, direct_consumer
required gates: format, compile app, focused unit tests, lint app, consumer contract test
profile: SCOPED
```

than to:

```text
touched app -> run everything
```

### `LEAN`

Use for docs/governance/metadata-only changes or cheap universal guards with no executable/product blast radius.

### `SCOPED`

Use for contained implementation changes: affected owner/module, direct consumers, focused tests, relevant lint/static analysis and compilation.

### `STRONG`

Use for cross-boundary or release-sensitive changes such as public/shared contracts, persistence/security, native/JNI, packaging/R8/manifest/dependency/variant behavior, lifecycle ownership or multi-owner integration.

`STRONG` means all evidence required by the material risk cone, not every repository job.

### `FULL`

Use when narrowing cannot be trusted or release policy deliberately requires complete validation.

Typical triggers are promotion/release, selector/global-build/dependency-inventory/toolchain changes, unknown executable paths or an explicit full request.

`FULL` should be exceptional on ordinary feature integration and absent from the normal edit loop.

## 4. Automatic gate selection

The default selector is `auto`.

It should:

- compare the intended base/head;
- identify changed owners and risk dimensions;
- expand to direct consumers/dependency cones only where required;
- map risks to concrete gates rather than only broad suites;
- treat docs-only/metadata-only work cheaply when safe;
- fail safe stronger for unknown executable paths;
- force `FULL` when the narrowing machinery itself changes;
- report risks, required gates, profile and reason.

Automatic escalation is allowed when additional risk is detected. Silent downgrade below the project selector is not.

An explicit stronger request is allowed. A weaker-than-auto override is exceptional and must be justified.

## 5. No-human-runner principle

An automatable deterministic gate MUST NOT be delegated to the user solely because the coding agent lacks local execution capability.

Incorrect:

```text
agent has no Android SDK -> ask user to run ./gradlew check
```

Correct:

```text
agent has no Android SDK
-> identify required Android gates
-> classify them REMOTE_AUTOMATED
-> reuse equivalent successful evidence when valid, otherwise trigger repository automation
-> inspect result/logs
-> fix owning cause
-> repeat only the invalidated gates
```

A user may explicitly choose a manual workaround, but repository procedure must not make that the default path.

## 6. Remote preflight and evidence reuse

Repositories expected to be maintained by execution-limited agents should expose an agent-triggerable remote preflight mechanism.

Before starting a new expensive run, preflight must look for successful existing evidence whose identity is equivalent for the required claim. Evidence identity normally includes:

- exact source head;
- intended target/base relationship;
- required gates;
- selected profile or stronger equivalent profile;
- selected E2E environment/fidelity when relevant.

PR number or UI object identity is **not** sufficient reason to rerun unchanged source evidence. Recreating a PR, changing draft/ready state or moving collaboration metadata does not invalidate equivalent evidence by itself.

Reuse is forbidden when the head changed, the material base relationship changed, required gates broadened, the previous profile was insufficient, the relevant E2E environment changed or the prior evidence is otherwise stale/invalid.

The normal algorithm is:

```text
required gates resolved
-> find equivalent successful evidence
-> reuse what remains valid
-> execute only missing/stale/insufficient gates
-> report one combined readiness result
```

Remote automation must preserve exact-head pinning, least privilege, no production/signing/deployment secrets in change-branch execution, bounded artifacts and project-owned command parity.

## 7. Readiness states

Publication readiness applies from `INTEGRATION`, not to every private edit, temporary branch push or draft collaboration update.

`preflight-change` reports:

- `READY_FOR_CI` — all required deterministic integration gates available agent-local passed; CI may confirm independently;
- `READY_FOR_REMOTE_PREFLIGHT` — semantic/base/diff/docs checks and available local gates passed, but missing required gates are `REMOTE_AUTOMATED`;
- `AUTOMATED_PREFLIGHT_CONFIRMED` — every required deterministic automated gate for the exact integration/release candidate is satisfied by valid current evidence, whether reused or newly executed;
- `NOT_READY_FOR_AUTOMATED_PREFLIGHT` — ambiguity, stale base/diff/docs, failed gate, missing automation, unsafe selection or another blocker prevents truthful readiness.

Real-environment evidence is tracked separately and may remain pending after automated preflight. It still blocks any stronger claim that depends on it.

## 8. Failure loop

For a failing deterministic gate:

```text
failure
-> inspect evidence
-> classify cause
-> identify violated invariant + owner
-> patch owning cause
-> re-evaluate risks/gates
-> invalidate only affected evidence
-> rerun/reuse as appropriate
```

Do not rerun a full suite merely because one focused gate changed unless the repair broadened the risk cone.

Do not suppress legitimate tests, downgrade selection to escape a failure or repeatedly patch symptoms without a new falsifiable hypothesis.

## 9. Validation economics

Validation is an engineering system with cost and signal.

Where practical, repositories should observe per-gate:

- duration;
- flake rate;
- unique regression signal;
- overlap/redundancy with other gates.

Use those observations to move high-signal cheap gates earlier and expensive low-frequency gates toward integration/release checkpoints without deleting evidence that protects a real invariant.

The target is not “fewer tests”. The target is:

> the cheapest feedback loop that preserves sufficient confidence at the current delivery stage.

If `FULL` runs frequently for contained changes, treat that as selector/design feedback. If a narrow gate repeatedly misses relevant defects, strengthen the risk-to-gate mapping.

## 10. Capability and scope gaps are repository defects

If a required deterministic gate is automatable but unavailable both locally and remotely, classify `AUTOMATION_CAPABILITY_GAP`.

If the repository cannot reliably determine affected risks/gates, classify `VALIDATION_SCOPE_GAP` and fail safe stronger while improving the selector.

The long-term fix is better automation and scope detection, not permanent human execution or full-CI-by-default.
