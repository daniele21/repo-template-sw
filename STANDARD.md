# Agent-Native Reference Engineering Standard

Version: 0.9.0

## Purpose

This standard defines the minimum engineering properties expected from software repositories maintained by humans and coding agents. It optimizes for correctness, operational simplicity, bounded resources, change safety, reproducibility, clean lifecycle behavior, product-experience quality where applicable, low context cost and **fast delivery at sufficient confidence**.

The central rule is:

> Make ownership, limits, failures and costs explicit, using the simplest solution that preserves the required invariants.

The delivery rule is:

> Optimize for sufficient confidence per unit of feedback time. Iterate cheaply, integrate rigorously, release comprehensively.

The automation rule is:

> Automation executes automatable work; humans make material decisions and provide evidence that genuinely requires a real environment.

The E2E rule is:

> Final target-environment validation should confirm residual environment-specific claims, not become the first complete-system test.

For products with a material UI:

> Make the user's next decision obvious, reveal complexity progressively, communicate state clearly, and keep the interface consistent, accessible and recoverable.

The standard is intentionally not a framework. Common semantics do not require common build, test, design or UI tools.

Focused normative contracts:

- [`OPERATING-CONTRACT.md`](OPERATING-CONTRACT.md) — command/build/artifact/runtime lifecycle semantics;
- [`EXECUTION-CAPABILITY-CONTRACT.md`](EXECUTION-CAPABILITY-CONTRACT.md) — delivery stages, risk-based validation, executor routing and evidence reuse;
- [`E2E-ENVIRONMENT-CONTRACT.md`](E2E-ENVIRONMENT-CONTRACT.md) — E2E environment fidelity and UI evidence modes;
- [`PRODUCT-EXPERIENCE-CONTRACT.md`](PRODUCT-EXPERIENCE-CONTRACT.md) — optional UX/UI semantics.

## 1. Delivery model

Delivery stage and validation depth are independent axes.

### Delivery stages

`ITERATION`

- default while implementation is changing;
- optimize for rapid falsification of the current edit;
- formatter/static checks, focused tests, affected compile/typecheck and direct-contract tests as needed;
- no automatic exact-head/full-diff/docs/preflight/release ceremony.

`INTEGRATION`

- begins when a coherent vertical slice produces an observable user/system outcome and is ready to converge;
- refresh exact head/base, review the complete diff, make affected durable documentation current and execute/rout the required risk gates;
- use the smallest affected critical E2E journey when lower-level evidence cannot prove the outcome.

`RELEASE`

- stable-branch promotion, release candidates and reference-grade checkpoints;
- `FULL` validation is expected;
- release-critical build/package/E2E and residual environment evidence apply.

### Validation depth

- `LEAN` — docs/governance/metadata or cheap universal guards;
- `SCOPED` — contained owner/module plus direct consumers/tests/lint/compile;
- `STRONG` — cross-boundary or release-sensitive risk cone;
- `FULL` — release/promotion or changes where safe narrowing cannot be trusted.

`FULL` is normal at release and exceptional during ordinary feature work.

The selector should identify **risk dimensions and concrete required gates first**, then summarize them with a profile. Profiles are not monolithic suite aliases.

## 2. Simplicity and ownership

Every dependency, abstraction, cache, worker, service, queue, layer and document adds maintenance and reasoning cost. Add one only for an observed or clearly specified problem.

Mutable state, public contracts, configuration values, persisted data, caches, significant resources, design tokens and durable decisions must have an identifiable owner. Avoid parallel sources of truth and duplicated policy.

Before changing a shared boundary, inspect its owner, direct consumers, fakes/adapters and nearby tests.

A repository should be understandable at a high level from its README, architecture document and accepted ADRs without broad historical ingestion.

## 3. Vertical outcomes and parallel work

Prefer changes that unlock an observable user/system outcome end to end.

A technical layer, adapter, ViewModel, migration helper or test harness is normally a **subtask** of a vertical slice unless it is independently valuable, mergeable and reviewable.

Parallel development does not imply stacked publication. Independent branches/worktrees may execute in parallel, but related work should converge early onto the coherent feature/integration outcome.

A PR whose only purpose is synchronizing a parent into a child is a coordination smell. Stacked PRs are justified only when each layer has independent integration/review value or separate ownership genuinely requires them.

## 4. Failure and recovery

Failure is normal behavior, not an exception to design.

Critical workflows define applicable behavior for:

- invalid input;
- partial initialization/dependency failure;
- timeout/cancellation;
- shutdown during work;
- restart/recovery;
- interrupted persistence/migration;
- failed build/package promotion;
- stale temporary/process/lock state.

For UI products, loading, empty, error, disabled, offline, permission and partial-result states are also normal product states.

A failing validation gate is evidence. Classify the cause and identify the violated invariant/owner before changing production code. Do not suppress a legitimate test or repeatedly patch symptoms without a new falsifiable hypothesis.

## 5. Resource lifecycle

For every significant resource define as applicable:

- owner/acquisition;
- lifetime/cardinality;
- memory/disk/CPU/GPU budget;
- concurrency/backpressure;
- timeout/cancellation;
- release/failure cleanup;
- idle/pressure behavior;
- observability.

No unbounded queue/list/cache is acceptable on an unbounded input path.

Processes, listeners, ports, locks, temp directories, build staging areas, test databases, browser/device sessions, logs, caches and evidence artifacts are resources too.

Cleanup must cover success, failure, timeout, cancellation, interruption and partial initialization. Cleanup may remove only resources whose ownership is established.

## 6. Automation and execution capability

Every required deterministic gate at integration/release is classified for the current agent/session as:

- `AGENT_LOCAL` — current agent can execute it directly;
- `REMOTE_AUTOMATED` — deterministic/automatable but unavailable in the current agent environment;
- `REAL_ENVIRONMENT` — genuinely requires representative hardware, protected authority/external environment or human judgement.

An ordinary compile/lint/test/R8/package gate does not become `REAL_ENVIRONMENT` merely because the current agent lacks the SDK.

A human must not become the fallback runner for automatable deterministic work.

When equivalent local execution exists, use it for faster feedback. When it does not, repository-owned remote automation is a valid execution backend.

## 7. Equivalent evidence reuse

At `INTEGRATION` and `RELEASE`, exact-head evidence must be current for the claim, but **unchanged evidence should not be rerun for collaboration metadata changes**.

Reuse successful evidence when it remains sufficient for:

- exact source head;
- material target/base relationship;
- required gates;
- selected profile or stronger equivalent;
- E2E environment/fidelity/evidence mode when relevant.

PR number, draft/ready state, labels and comments are not source-evidence identity by themselves.

Rerun only missing, stale or insufficient evidence.

## 8. Environment fidelity

Execution capability and environment fidelity are independent dimensions.

A CI Android emulator can be `REMOTE_AUTOMATED` while only `simulated_or_emulated`. A physical device farm can also be `REMOTE_AUTOMATED` while providing `representative_physical` evidence.

Critical E2E journeys declare target environments, automated execution environments and fidelity gaps in `.engineering/e2e.json`.

Use the cheapest automated environment sufficient for the changed claim, then escalate only when a material target dimension requires it.

## 9. E2E evidence

E2E proves a complete critical user/system outcome across assembled boundaries when lower-level tests cannot establish it. Keep critical journeys small and high-value.

Do not move deterministic logic coverage into E2E merely because an E2E framework exists.

For UI-bearing journeys, evidence strength follows the actual claim:

- `ASSERTIONS` — UI is incidental to deterministic system behavior;
- `SCREENSHOTS` — stable visible layout/hierarchy/copy/state/recovery/adaptive semantics changed;
- `FULL_MEDIA` — motion, timing/progression, navigation/transition sequence, lifecycle visibility, gesture continuity or release/product acceptance depends on observing the journey over time.

UI presence alone does not require video.

A run is `E2E_EVIDENCE_INCOMPLETE` when evidence required by the **selected mode** is missing. Never silently downgrade the selected mode after execution to obtain a green result.

All E2E evidence is identity-bearing, privacy-safe and bounded-retention. Emulator/simulator evidence cannot satisfy a physical/target-environment claim.

## 10. Build and artifact lifecycle

Material builds have unique build identity distinct from product version. Successful distributable artifacts are immutable and promoted only after validation.

Where applicable, successful artifacts carry:

- source/build identity;
- manifest;
- checksum;
- generated build delta against the previous successful comparable build;
- bounded lineage-aware retention.

Failed or partial artifacts must not be placed where they can be mistaken for successful outputs.

Local runtimes and build/test/E2E operations leave no unintended project-owned process, listener, lock, temporary state or resource residue.

Detailed semantics live in `OPERATING-CONTRACT.md`.

## 11. Security and data

Repositories document trust boundaries and sensitive-data lifecycle.

For persisted/user-sensitive data define creation, storage, encryption/trust boundary, retention, deletion, migration/recovery, backup/export when applicable and logging restrictions.

Never introduce silent cloud fallback, secret persistence, sensitive payload logging or destructive migration behavior without an explicit contract/review.

Remote execution of change-branch code uses least privilege and does not gain production/signing/deployment secrets merely for convenience.

## 12. Product experience

When `product-ui` is adopted, meaningful product-experience work follows at proportional depth:

```text
user outcome
-> task model
-> information architecture / critical journey
-> information + action hierarchy
-> progressive disclosure / defaults
-> interactions / states / feedback / recovery
-> adaptive / platform behavior
-> accessibility
-> design system / components
-> motion
-> visual polish / graphics
-> validation
```

Structure precedes polish. Motion has a product purpose. The canonical design system/source of truth is reused rather than duplicated.

Accessibility, adaptive behavior, recovery and usability are separate claims from visual appearance.

## 13. Documentation and agent context

Git is implementation history. Durable docs describe the system that exists now.

- `AGENTS.md` — bounded routing/invariants;
- `.engineering/commands.json` — operation/development-velocity/execution routing;
- `.engineering/e2e.json` — E2E environment/evidence routing;
- architecture/feature/ADR docs — durable current truth;
- `docs/current-state.md` — integrated/blocked/next repository truth;
- `docs/workstreams/` — active bounded coordination only;
- Skills — conditional recurring procedures;
- scripts/CI — deterministic enforcement/execution.

During `ITERATION`, affected durable documentation may remain pending while behavior changes. Before `INTEGRATION`, every affected canonical documentation owner must be current with the candidate.

Do not update `current-state.md` for every agent commit or branch synchronization.

Completed workstream plans are deleted by default after durable knowledge transfer; Git retains history.

Machines should enforce what machines can check. Avoid spending agent context repeating deterministic rules already enforced by scripts/CI.

## 14. Validation economics

Where practical, observe validation gates for:

- duration;
- flake rate;
- unique regression signal;
- overlap with other gates.

Use this evidence to move cheap high-signal checks earlier and expensive low-frequency checks toward integration/release checkpoints.

This is not a mandate to delete tests. It is a mandate to place each test where its confidence contribution justifies its feedback cost.

If `FULL` runs frequently for contained changes, improve scope/risk selection. If narrow validation repeatedly misses affected regressions, strengthen the risk-to-gate mapping.

## 15. Maturity levels

### L0 — Healthy repository

At minimum:

- clear purpose and architecture/ownership;
- bounded agent routing/context;
- reproducible setup and pinned/locked dependencies where applicable;
- machine-readable project operating/development-velocity contract;
- deterministic formatting/static/test/build validation appropriate to the stack;
- explicit risk-based validation routing and no-human-runner semantics;
- explicit E2E applicability/environment contract;
- build/artifact/resource cleanup invariants where applicable;
- security/trust-boundary documentation;
- repository hygiene and no secret/private/generated artifact leakage;
- bounded active workstreams/current-state documentation;
- product-experience contracts when `product-ui` applies.

### L1 — Production ready

L0 plus:

- integration/contract tests for critical boundaries;
- bounded high-value automated E2E where full workflow evidence is needed;
- target/fidelity gaps declared and residual real-environment confirmation identified;
- migration/backward-compatibility strategy where state/contracts persist;
- critical failure/cancellation/recovery coverage;
- performance/resource budgets for important paths;
- useful observability and release/rollback procedures;
- immutable traceable distributable artifacts;
- least-privilege remote automation;
- accessibility/adaptive/recovery evidence for material UI workflows.

### L2 — Reference grade

L1 plus:

- architecture fitness functions for critical ownership/dependency invariants;
- resource/memory/performance regression gates where stable measurement is possible;
- fault/pressure coverage for important lifecycle boundaries;
- high-value critical journeys at the highest practical automated fidelity before residual target testing;
- representative hardware/device evidence when hardware materially changes behavior;
- machine-enforced docs/context/operating/E2E/product-experience health;
- periodic validation-economics review so avoidable cost/flake/overlap is reduced;
- explicit complexity/dependency review for meaningful additions;
- active control of stale/duplicate documentation and design-system drift.

L2 is a target, not an excuse to add machinery the project does not need.

## Completion rule

A change is not “done” because every possible test ran.

It is done at the relevant stage when:

- the intended observable outcome is correct;
- changed owners/contracts/failure/resource semantics agree;
- the narrowest sufficient required evidence for that stage is satisfied;
- affected durable documentation is current before integration/release;
- stronger residual environment/release evidence remains explicit rather than falsely claimed.

The engineering objective is **high-confidence incremental delivery without validation waterfall**.
