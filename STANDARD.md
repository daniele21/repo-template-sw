# Agent-Native Reference Engineering Standard

Version: 0.8.0

## Purpose

This standard defines the minimum engineering properties expected from software repositories maintained by humans and coding agents. It optimizes for software correctness, operational simplicity, bounded resource use, change safety, reproducibility, clean lifecycle behavior, product-experience quality where applicable, low context cost, and high-confidence delivery through agent-accessible automation.

The central rule is:

> Make ownership, limits, failures and costs explicit, using the simplest solution that preserves the required invariants.

The operational corollary is:

> Every operation must be identifiable, owned, bounded, reversible and leave no unintended residue.

The delivery corollary is:

> Automation executes automatable work; humans make material decisions and provide genuinely real-environment evidence. CI should confirm deterministic validation when the agent has an equivalent local environment, and act as a remote execution backend when it does not.

The E2E corollary is:

> Final target-environment validation should confirm residual environment-specific claims, not become the first time the complete workflow is exercised.

For products with a material UI:

> Make the user's next decision obvious, reveal complexity progressively, communicate state clearly, and keep the interface consistent, accessible and recoverable.

The standard is intentionally not a framework. A project should adopt only the mechanisms justified by its real requirements. Common semantics do not require common build, test, design or UI tools.

Detailed operational semantics live in [`OPERATING-CONTRACT.md`](OPERATING-CONTRACT.md). E2E target-environment and fidelity semantics live in [`E2E-ENVIRONMENT-CONTRACT.md`](E2E-ENVIRONMENT-CONTRACT.md). Validation execution ownership across agent-local, remote-automated and real-environment contexts is defined in [`EXECUTION-CAPABILITY-CONTRACT.md`](EXECUTION-CAPABILITY-CONTRACT.md). UI products may additionally adopt [`PRODUCT-EXPERIENCE-CONTRACT.md`](PRODUCT-EXPERIENCE-CONTRACT.md) through the optional `product-ui` profile.

## Operating principles

### Simplicity is a budget

Every dependency, abstraction, cache, worker, service, queue, layer and document adds maintenance and reasoning cost. Add one only when it solves an observed or clearly specified problem. Prefer direct code over speculative extensibility. Redesign a bad boundary rather than stacking compensating abstractions over it.

### One owner for every important fact

Mutable state, public contracts, configuration values, persisted data, caches, significant resources, design tokens and canonical product/design decisions must have an identifiable owner. Avoid parallel sources of truth, scattered globals and duplicated policy.

### Failure is part of the design

Success, invalid input, partial failure, timeout, cancellation, shutdown and cleanup are normal lifecycle paths. Critical components define and test each applicable path.

For UI products, loading, empty, error, disabled, offline, permission and partial-result states are also normal product states rather than afterthoughts.

### Resources are contracts

For every significant resource, define owner, acquisition, lifetime, maximum cardinality, budget, concurrency, backpressure, timeout, cancellation, release, cleanup, idle/pressure behavior and observability. OOM is not a resource-management strategy.

Processes, listeners, ports, locks, temp directories, build staging areas, test databases, browser/device sessions, logs and caches are resources too. Temporary does not mean ownerless.

### Clean lifecycle is a contract

A successful, failed, timed-out, cancelled or interrupted operation must restore all applicable project-owned temporary state. Repeated runs should not behave differently because previous runs left processes, listeners, locks, stale artifacts, test/browser/device state, temp data or incompatible caches behind.

### Progressive disclosure applies to people too

Technical/product complexity should be exposed according to the user's current decision. Essential controls should not compete visually with advanced configuration, raw diagnostics or expert/debug surfaces unless those are central to the user's task.

The UI should model the user's task rather than forcing users to understand internal architecture.

### Machines enforce what machines can check

Do not spend agent context reminding a model about rules that deterministic tooling can enforce. Formatting, architecture boundaries, document budgets, token budgets, command-contract shape, E2E-environment contract shape, product-experience contract shape, tests, generated-artifact bans and similar invariants belong in scripts and automated validation where practical.

### Automation executes; humans decide

Every required validation gate is classified for the current agent/session as `AGENT_LOCAL`, `REMOTE_AUTOMATED` or `REAL_ENVIRONMENT`.

When the current agent has an equivalent local execution environment, deterministic formatter/lint/compile/test/build failures should be falsified locally before CI. In that situation CI should confirm rather than become the normal edit-test loop.

When the current agent lacks the required checkout, shell, SDK, platform toolchain, service or compute capability, an otherwise deterministic automatable gate becomes `REMOTE_AUTOMATED`. Repository-owned remote validation — including CI when appropriate — becomes the execution backend. The user must not become the fallback runner merely because the agent lacks local execution capability.

Only evidence that genuinely depends on representative hardware, protected authority, an external environment or manual judgement belongs in `REAL_ENVIRONMENT`.

A deterministic failure found remotely is process feedback only when an equivalent agent-local environment existed and should have found it. When no equivalent local capability existed, remote discovery is valid execution.

### Environment fidelity is a separate axis

Execution capability answers who/where can run a gate. Environment fidelity answers how closely the environment used by that gate represents the target relevant to the claim. These must not be conflated.

A CI emulator can be `REMOTE_AUTOMATED` while only providing simulated/emulated fidelity. An automated physical-device farm can also be `REMOTE_AUTOMATED` while providing representative-physical evidence. A manual run on the actual supported target can be `REAL_ENVIRONMENT` and target-environment evidence.

Critical E2E journeys declare their target environment, automated execution environments and known fidelity gaps in `.engineering/e2e.json`. Prefer the cheapest sufficient automated environment during iteration, then escalate fidelity only when a material target dimension requires it. Final real-environment testing should primarily close the residual fidelity gap rather than discover ordinary whole-system defects that could have been automated earlier.

### Git is history; docs describe the system that exists

Active documentation explains current behavior, durable decisions, operations and active work. Completed implementation plans are deleted by default after durable knowledge is transferred. Archive only material with independent audit, regulatory, release or historical value.

### Progressive disclosure for agents

Agents start with the root `AGENTS.md`, then the closest scoped guide, then the single canonical document required by the task, then code and nearby tests. A repository should not require broad documentation ingestion before a local change.

## Maturity levels

### L0 — Healthy repository

Required before a project is considered engineering-grade:

- clear README and repository purpose;
- explicit architecture/ownership map;
- root `AGENTS.md` with bounded context and routing;
- pinned/locked dependencies and reproducible setup where applicable;
- a project-local operating command contract mapping canonical intents to native tooling;
- a pre-publication readiness gate requiring execution-capability classification, complete-diff review, material-ambiguity resolution, target-base freshness and deterministic automated validation;
- an agent-triggerable remote validation path when supported coding agents may lack equivalent local execution capability for required deterministic gates;
- formatting, lint/static checks, tests and build validation appropriate to the stack;
- E2E applicability is explicitly decided rather than accidentally absent;
- E2E-applicable repositories explicitly declare target environments, execution environments, fidelity classes/gaps and critical journeys rather than treating all E2E environments as equivalent;
- material builds have unique build/source identity and do not silently overwrite prior builds;
- local generated/build artifacts are bounded and do not accumulate indefinitely;
- local runtimes/processes/listeners and other ephemeral resources have deterministic cleanup where applicable;
- CI on pull requests and protected canonical branches;
- explicit configuration and no committed secrets;
- bounded resources, queues and caches where applicable;
- timeouts, cancellation and cleanup for long-lived operations;
- structured/error-classified logging without sensitive payloads by default;
- security/trust-boundary documentation;
- repository hygiene: no generated build output, large model/media artifacts or private local state unless explicitly justified;
- one current-state ledger and bounded active workstreams;
- no completed implementation plans kept as active documentation;
- when `product-ui` is adopted: design/brand source of truth, critical journeys, progressive disclosure/action hierarchy, critical loading/empty/error/disabled states, accessibility target, adaptive-layout scope, semantic design-system ownership and key reference views are explicitly defined.

### L1 — Production ready

L0 plus:

- integration/contract tests for critical internal boundaries;
- automated E2E evidence for critical workflows when lower-level tests cannot establish the complete user/system outcome;
- critical E2E journeys are intentionally small/high-value rather than broad UI-script coverage for its own sake;
- critical E2E journeys map the relevant target environment to one or more automated environments and explicitly record residual fidelity gaps;
- target/real-environment confirmation is required only where the product claim depends on dimensions automation cannot truthfully reproduce at sufficient fidelity;
- migration and backward-compatibility strategy where data/contracts persist;
- failure, cancellation and shutdown tests for critical lifecycle components;
- backup/restore or recovery procedures where user/business data requires them;
- performance and resource budgets for important paths;
- observability sufficient to answer what is running, why, how long, resource use and failure cause;
- release procedure, rollback strategy and operational runbooks;
- successful distributable artifacts have manifests/checksums and durable release storage appropriate to the project;
- build deltas are generated for material comparable builds when artifacts are distributed/tested across runs;
- E2E failure evidence is identity-bearing, privacy-safe and stored with bounded retention;
- dependency/security scanning appropriate to the threat model;
- real environment evidence for behavior that cannot be truthfully validated through automation;
- deterministic automated jobs invoke the same canonical project-owned validation semantics regardless of whether execution is agent-local or remote;
- remote execution of change-branch code follows a least-privilege trust model and does not expose production/signing/deployment secrets;
- when `product-ui` is adopted: critical journeys have appropriate UX/E2E evidence, high-value adaptive layouts are tested, accessibility has suitable automated/manual evidence, user-facing failures provide actionable recovery, and stable high-risk visual surfaces use regression protection where valuable.

### L2 — Reference grade

L1 plus:

- architecture fitness functions for critical dependency/ownership invariants;
- resource and memory regression tests;
- performance regression gates for critical paths where stable measurement is possible;
- pressure/fault-injection coverage for important lifecycle boundaries;
- repeatability/cleanliness evidence for important dev/test/e2e/build/smoke/runtime lifecycles;
- critical E2E journeys include representative failure/retry/recovery paths where those paths materially affect product correctness;
- E2E runs exercise the real built/package/device surface when the stronger product claim depends on it and this is technically practical;
- the highest practical automated environment fidelity is used before final target validation for high-value critical journeys, so residual manual/device evidence is intentionally narrow;
- representative device/hardware evidence when hardware materially changes behavior;
- machine-enforced documentation, agent-context, operating-contract, E2E-environment-contract and applicable product-experience checks;
- explicit complexity/dependency review for meaningful additions;
- reproducible benchmark/evidence identity where results influence engineering decisions;
- automated repository policy/health validation;
- automated first-pass health is measured or periodically reviewed so recurring avoidable formatting/compile/test/base-drift failures are moved to the earliest executor that can reproduce them;
- repeated target-environment discoveries that are reproducible in a declared automated environment are treated as E2E-fidelity gaps and moved earlier;
- execution-capability gaps are reviewed so supported coding agents do not require humans to run ordinary deterministic suites;
- stale/duplicate documentation and completed-work detection;
- when `product-ui` is adopted: important/high-risk workflows have representative-user usability evidence when justified, critical UX regressions are protected appropriately, and design-system/token/component drift is actively controlled.

L2 is a target, not an excuse to add machinery that a project does not need.

## Architecture and ownership

A repository should be understandable at a high level in roughly fifteen minutes from its README, architecture document and accepted ADRs.

Prefer a flow such as:

`Input -> boundary/adapter -> domain/core -> infrastructure -> output`

without requiring that exact layering when a simpler design is sufficient.

Public/domain contracts should not leak infrastructure-specific handles or implementation types. Composition roots assemble long-lived services. Domain policy belongs with the domain owner rather than UI, transport or persistence adapters.

Before changing a boundary, inspect its owner, direct consumers, fakes and tests.

## Complexity and dependencies

New complexity must answer:

1. Which concrete problem does this solve?
2. Why is the existing owner/boundary insufficient?
3. What new failure/resource/upgrade surface is introduced?
4. Can a simpler local solution preserve the same invariants?
5. How will we know later whether this complexity is still justified?

Dependencies are liabilities as well as capabilities. Pin and lock them. Avoid dynamic `latest` versions. Prefer isolated dependency updates. Do not add a framework for a problem that a small, clear component can solve safely.

## Resource lifecycle contract

For every significant resource — model, tokenizer, KV cache, audio/video buffer, worker, thread, process, socket, HTTP client, database connection, temporary file, cache, job queue, build workspace, test browser/device session, lock or similar — define as applicable:

- Owner
- Acquisition
- Lifetime
- Maximum cardinality
- Memory/disk/CPU/GPU budget
- Concurrency policy
- Backpressure policy
- Timeout
- Cancellation
- Release
- Failure cleanup
- Idle policy
- Pressure policy
- Metrics

No unbounded queue/list/cache is acceptable on an unbounded input path. Admission should happen before expensive allocation when resource exhaustion is predictable. Active/pinned resources must be protected from incompatible eviction or unload.

Temporary resources must be cleaned on success, failure, timeout, cancellation, interrupt and partial initialization. Cleanup may remove only resources whose ownership is established.

## Concurrency and backpressure

Define maxima for requests, workers and expensive operations. Define what happens at capacity: queue, reject, wait, degrade or evict. Do not spawn indefinitely. Queue capacity and timeout semantics must be explicit and observable.

Parallel dev/test/e2e/build runs should use isolated run identities/workspaces when shared mutable temporary state would otherwise collide.

## Failure and recovery

Critical workflows should define behavior for success, invalid input, partial initialization, dependency failure, timeout, cancellation, process/app shutdown and restart/recovery where applicable.

Test failure at the owning boundary. Useful scenarios include partial load, corrupt input, disk full, interrupted write, process death, cancellation during work, shutdown during active work, stale PID/lock/temp state and failed build/package staging.

Partial or failed build artifacts must not be promoted to locations where they can be mistaken for valid outputs.

A validation failure is evidence, not an instruction to patch the nearest line. Classify the failure, identify the violated invariant and its owner, distinguish branch-induced regressions from pre-existing/environment/flaky failures, then fix the owning cause. Repeated failures of the same gate after attempted fixes require re-evaluating the design/assumptions before another patch.

## Project operating contract

Each adopted repository declares its actual operational mapping in `.engineering/commands.json`. The common vocabulary is:

`setup -> doctor -> dev -> check -> test -> e2e -> build -> smoke -> package -> stop -> clean`

Not every intent is applicable to every project; genuinely irrelevant commands may be declared `n/a`. Do not introduce a generic wrapper merely to force identical command syntax. The project should map these intents to its native Gradle, Xcode, Swift, Python, Node, browser-test, shell or other established tooling.

The same contract declares the repository publication gate, validation execution model and optional/required remote-preflight trigger. It does not require a universal local wrapper; agents select the applicable canonical project commands and execute them through the earliest capable automated environment.

The detailed normative behavior for commands, pre-publication readiness, E2E, build identity, artifact lineage/lifecycle, build deltas, local runtimes, ports/processes and zero-residue cleanup is defined in [`OPERATING-CONTRACT.md`](OPERATING-CONTRACT.md). E2E target-environment/fidelity selection is defined in [`E2E-ENVIRONMENT-CONTRACT.md`](E2E-ENVIRONMENT-CONTRACT.md) and `.engineering/e2e.json`. Executor selection and no-human-runner semantics are defined in [`EXECUTION-CAPABILITY-CONTRACT.md`](EXECUTION-CAPABILITY-CONTRACT.md).

### Pre-publication readiness

Before publishing a change for automated validation, a coding agent must establish an exact-head readiness state:

- resolve any material ambiguity that could change product behavior, public contracts, persistence, security/trust boundaries, resource/concurrency semantics, backward compatibility, acceptance criteria or meaningful UX;
- verify ownership and inspect the complete diff rather than only the last edited files;
- synchronize or otherwise verify against the current intended target base; stacked work remains explicitly conditional until its dependency is integrated/replayed;
- select every deterministic gate required by blast radius and classify it as `AGENT_LOCAL`, `REMOTE_AUTOMATED` or `REAL_ENVIRONMENT` for the current agent/session;
- when E2E is required, select the affected critical journey and cheapest sufficient declared environment fidelity separately from executor classification;
- execute all required `AGENT_LOCAL` gates directly;
- route all required `REMOTE_AUTOMATED` gates through repository-owned automation rather than asking the user to execute them;
- classify every failure and repair its owning cause instead of weakening/suppressing gates or applying unexplained symptom patches;
- record PASS/FAIL/PENDING/N/A truthfully and keep real-environment evidence distinct from automatable validation.

`READY_FOR_CI` applies when the current agent could execute every required deterministic gate locally and did so successfully. `READY_FOR_REMOTE_PREFLIGHT` applies when semantic/base/diff checks and available local gates pass but required deterministic gates need remote automation. `AUTOMATED_PREFLIGHT_CONFIRMED` means all required deterministic automated gates passed on the exact current head/base at the required declared E2E fidelity, regardless of execution location.

Changing the head, material target-base relationship or a target/environment assumption relevant to the claim invalidates prior affected readiness evidence.

### E2E validation

`e2e` is a complete workflow validation boundary, not a synonym for all tests and not a synonym for smoke.

Use E2E when correctness depends on the assembled system and cannot be established adequately by unit/integration/contract tests alone. Keep most invariants in lower-level tests and reserve E2E for a small set of critical journeys.

E2E-applicable repositories declare target environments, execution environments, fidelity classes/gaps and critical-journey mappings in `.engineering/e2e.json`. Execution capability and environment fidelity remain separate axes: a remote automated emulator is not physical-device evidence simply because CI executed it.

Prefer the cheapest automated environment that can prove the claim during normal iteration. Escalate to built/package execution and stronger virtual/physical fidelity when the changed invariant depends on those dimensions. Final target/real-environment validation should primarily close residual fidelity gaps that cannot truthfully be automated earlier.

The standard does not mandate one framework. Prefer stack-native tooling. Browser/web projects should generally prefer Playwright unless an equally strong established solution already exists; native mobile/desktop and server/CLI projects should use the appropriate native or protocol-level equivalent.

E2E runs must have deterministic cleanup and bounded failure evidence. When the claim concerns a distributable artifact, run E2E against the built/package artifact when technically practical.

### Build identity

A material build receives a unique build ID even when rebuilding the same source revision. Product/release version is distinct from build identity.

Artifact identity should include or carry product version, build ID and source revision, plus platform/architecture/channel/variant lineage where applicable. Dirty source state must be distinguishable when local builds permit it.

A new build must not silently overwrite a previous successful build.

### Artifact lifecycle

Successful artifacts are immutable. Build/package output is produced in staging, validated, then promoted. Durable binary/package artifacts include a machine-readable build manifest and SHA-256 checksum where applicable.

The default local retention is the latest two successful builds per comparable artifact lineage. CI/E2E artifacts are temporary evidence with explicit bounded retention. Durable releases belong in GitHub Releases or an equivalent release/artifact store; package/container registries are used when the output is genuinely consumed as a package/container.

### Build delta

Every successful material build should generate a `BUILD_CHANGELOG.md` or equivalent delta against the previous successful comparable build in the same lineage.

The delta covers source, dependencies, toolchain, configuration, compatibility/migrations, artifact metrics and validation, including relevant E2E evidence, where applicable. A generic Git log is insufficient because rebuilds can differ without source changes.

### Local runtime and zero residue

Local servers bind to loopback by default unless external exposure is intentional. Ports are configurable and collision-checked. Project-owned processes/listeners have explicit shutdown ownership.

After `stop`, E2E/smoke cleanup, timeout, failure or interrupt, no project-owned application listener or orphan process may remain. Normal kernel states such as TCP `TIME_WAIT` are not considered an open project listener.

Dev/test/e2e/build/smoke/package tooling must also clean owned locks, temp files/directories, test databases, browser/device sessions, downloads, run-scoped logs, reservations and other ephemeral resources.

## Product experience contract

Repositories with a material UI should adopt the optional `product-ui` profile and specialize `design/ux-contract.json` plus `design/brand-kit.json`.

The product experience contract standardizes experience quality rather than aesthetics. It requires intentional information architecture, progressive disclosure, bounded cognitive load, sensible defaults, complete states/feedback, actionable error recovery, accessibility, adaptive layout, design-system/brand ownership, critical journeys and appropriate UX regression evidence.

Meaningful product-experience work follows semantic dependency order rather than starting from visual treatment:

`user outcome -> task model -> information architecture / critical journey -> information + action hierarchy -> progressive disclosure / defaults -> interactions + states + feedback + recovery -> adaptive / platform behavior -> accessibility -> design system -> motion -> visual / graphics -> validation`

The depth of this reasoning is proportional to semantic impact. Structural UX changes traverse the full sequence; interaction changes start at the earliest affected dependency while checking upstream assumptions; genuinely visual-only changes may stay at design-system/visual validation when task, flow, hierarchy and interaction semantics are unchanged.

Motion, illustration, gradients, effects and other visual treatments must not compensate for unresolved task-model, hierarchy, flow or interaction problems. Motion should communicate feedback, continuity, spatial relationship, state change, progress, attention or another explicit experience purpose before its aesthetic language is chosen.

The UI should model user goals rather than internal architecture. Advanced/debug/diagnostic controls remain discoverable but should not dominate normal flows unless they are genuinely central to the user's job.

Brand identity should use semantic design tokens and a declared source of truth. A design system should reuse canonical semantic components before creating visually duplicative one-offs. Mockups/key reference views are maintained as bounded product references, not an uncontrolled parallel implementation history.

Web products should target WCAG 2.2 AA or a stronger declared target; native products should use equivalent platform accessibility semantics and evidence.

Responsive/adaptive behavior should preserve content priority across supported device/window contexts rather than merely shrink a desktop layout.

Significant UI changes should validate the narrowest applicable evidence: component/state behavior, critical-journey E2E, accessibility, visual regression for stable high-value surfaces, and representative-user usability evidence where the risk/value justifies it.

Detailed semantics live in [`PRODUCT-EXPERIENCE-CONTRACT.md`](PRODUCT-EXPERIENCE-CONTRACT.md).

## Data lifecycle and security

For each meaningful data category define creation, storage location, owner, encryption/trust boundary, retention, deletion, backup, export, logging, migration and recovery.

Defaults should minimize exposure. Remote/network access must be explicit when local-only behavior is expected. Never silently fall back to cloud processing. Secrets, private user paths and sensitive content do not belong in source control or normal telemetry.

Build/test/E2E tooling must not leak generated credentials, signing material, private data or sensitive screenshots/traces into logs, caches or distributed artifacts.

## Observability

Operational evidence should answer:

- what is the system doing;
- why is it doing it;
- which operation/request/job/run owns the work;
- how long has it taken;
- what resources are consumed;
- why did it fail or degrade;
- which build/artifact/runtime identity is involved when relevant.

Use truthful metric names, units and sources. Unavailable data remains unavailable rather than becoming zero. Keep correlation identifiers privacy-safe. For local AI systems, consider resident models, memory/VRAM or unified memory, queue depth, active jobs, load time, time-to-first-token, throughput, cache hits and eviction reason when applicable.

For UI products, system state should be observable to users at the level they need for the current task, while deeper diagnostics remain progressively disclosed.

## Testing

Optimize for tested invariants rather than an arbitrary coverage percentage. Every critical invariant should have a deterministic test when technically possible.

Use a layered strategy:

- unit/component tests for local behavior and invariants;
- integration/contract tests for real boundary interactions;
- E2E tests for complete critical workflows whose outcome depends on the assembled system;
- smoke tests for minimal viability of the built/running artifact;
- accessibility/visual/usability evidence for UI claims when applicable.

Do not shift deterministic low-level behavior into E2E merely because E2E feels more realistic. Prefer the cheapest test level capable of proving the claim.

For E2E, also prefer the cheapest environment fidelity capable of proving the changed claim. Escalate from host/fake or simulated/emulated environments to representative virtual/physical/target environments only when a material target dimension requires it. Keep residual target-environment evidence explicit.

Examples:

- an active resource cannot be evicted;
- cancellation releases ownership and reservations;
- unsupported combinations fail before backend execution;
- a proposed financial/import record is not canonical before required review;
- cloud processing is never implicit;
- backup/export round trips preserve required data;
- repeated lifecycle operations do not leak resident resources;
- a critical create/use/save/reopen journey works end to end when that is a product-critical flow;
- E2E failure cleanup leaves no project-owned server/browser/helper/temp residue;
- E2E evidence reports the environment/fidelity actually exercised and does not overclaim a stronger device/target result;
- loading/empty/error/disabled states remain usable and understandable on critical UI flows;
- keyboard/focus/accessibility semantics remain valid where applicable;
- start -> smoke -> stop leaves no project-owned listener/process/temp residue;
- failed builds are not promoted as successful artifacts;
- local artifact retention remains bounded.

Use the narrowest useful test loop while iterating, then expand validation according to change scope. Before publication, `preflight-change` converts those scoped results into exact-head readiness evidence, selects required E2E journey/environment fidelity when applicable, classifies required gates by execution capability, runs available local gates and routes unavailable deterministic work through `remote-preflight`.

When a test fails, do not immediately mutate production code. First establish whether the failure is caused by the current change, already exists on the target base, is environment/toolchain-specific, is flaky/non-deterministic, or exposes an incorrect assumption/contract. Fix the owner of the violated invariant and add regression evidence at the lowest useful level.

## Performance

Important projects should define measurable budgets appropriate to their product: startup, idle and peak memory, latency/percentiles, throughput, shutdown, binary size, storage growth or queue wait. Measure before optimizing and avoid performance claims without representative evidence.

For UI products, perceived performance and feedback matter too: long operations should communicate meaningful state/progress when available rather than leaving the user uncertain.

Artifact/build deltas should surface meaningful size/performance changes when they materially affect product quality.

## Reproducibility

A clean checkout should have a documented path to setup, test, E2E when applicable, build and run. Pin toolchains where practical, commit lockfiles, validate configuration and avoid environment-specific hidden state. Benchmark/evidence artifacts used for decisions should include enough identity to be reproduced.

E2E-applicable repositories should make `.engineering/e2e.json` sufficient to identify which target/execution environments and fidelity gaps are relevant to each critical journey without relying on unwritten tribal knowledge.

Material build manifests should identify source revision and enough toolchain/configuration context to diagnose why two builds differ. Local/global environment pollution should be avoided; prefer project-scoped environments and explicit configuration.

## Repository hygiene

Git should contain source, tests, configuration, small fixtures, durable documentation and small durable assets. Prefer release assets, artifact storage or LFS when justified for large binaries/media. Generated bundles, build output, model weights, logs, caches, private data and temporary evidence should not accumulate in normal source history.

Local artifact directories are bounded convenience stores, not release registries. E2E/visual-regression traces/screenshots/videos are bounded evidence artifacts, not source history. Keep only deliberate key design/reference views; do not accumulate uncontrolled mockup revisions. `clean` removes only project-owned generated state. Cache/log retention is bounded where material.

## Documentation lifecycle

Use one canonical owner for each durable fact. Prefer links over duplicate detailed descriptions.

Recommended active documentation:

- `docs/architecture.md`: current boundaries and ownership;
- `docs/features/`: durable current behavior when additional explanation is useful;
- `docs/adr/`: durable architectural decisions and their rationale;
- `docs/current-state.md`: short volatile repository-level status;
- `docs/workstreams/`: only active, bounded implementation plans;
- `design/`: product-experience contracts and bounded key references when `product-ui` is adopted;
- `.engineering/e2e.json`: machine-readable current E2E target/execution environment and critical-journey mapping;
- runbooks/evidence docs only when the project requires them.

A completed workstream follows:

`plan -> implement -> validate -> transfer durable knowledge -> delete plan`

Do not create a document solely to record that a PR or isolated implementation step completed. Generated per-build deltas and per-run E2E/visual evidence are artifact metadata/evidence, not active project-planning documentation.

## Agent-operability

### Root guide

`AGENTS.md` is a routing layer, not a repository encyclopedia. It contains only durable repository-wide invariants, ownership/routing, task reading rules and validation selection. It points to `.engineering/commands.json` for canonical operational commands, `.engineering/e2e.json` for E2E environment/fidelity routing and to `design/ux-contract.json` for UI-product experience constraints when `product-ui` is adopted.

### Scoped guides

Add a scoped `AGENTS.md` only when a subtree has meaningful local invariants, hazards, ownership or validation commands. The closest applicable guide should let an agent avoid loading unrelated domains.

### Material ambiguity

Coding agents should resolve ambiguity from canonical repository evidence before asking the user. An ambiguity is material when two reasonable interpretations would produce meaningfully different product behavior, public/API contracts, persisted data semantics, trust/security boundaries, resource/concurrency behavior, backward compatibility, acceptance criteria or user experience.

For material ambiguity, inspect the owner, durable docs/ADRs, direct consumers and tests. If one interpretation is not established, ask the user before implementation and present the smallest useful decision with a recommendation when possible. Do not ask about implementation-local naming/style choices that do not alter observable semantics.

If interaction with the user is unavailable, do not silently convert a material product/contract decision into an implementation assumption. Mark the work blocked/conditional at that boundary and continue only with independent work that does not depend on the unresolved choice.

### Context budgets

Default recommended budgets:

- root `AGENTS.md`: <= 2,500 estimated tokens;
- scoped `AGENTS.md`: <= 2,000 estimated tokens;
- `docs/current-state.md`: <= 1,500 estimated tokens;
- one active workstream: <= 3,000 estimated tokens;
- bootstrap bundle (root guide + required routing context): target <= 2,500 tokens;
- root + scoped guide + one active workstream: target <= 6,000 tokens before code/tests.

Projects may tighten these budgets. Raising them requires a concrete justification.

### Workstreams as DAGs

Active plans are compact execution structures, not narrative diaries. They identify goal, non-goals, invariants, work items, dependencies, parallel lanes, state, acceptance criteria, validation and durable documentation destinations.

Status lives in the workstream table itself. Avoid separate plan/progress/status documents for the same work.

### Skills

Put conditional workflows in Skills rather than the root guide. Put facts/behavior in documentation. Put deterministic rules in scripts/CI.

Core reusable Skills are:

- `plan-workstream`;
- `structured-change`;
- `design-product-experience`;
- `validate-change`;
- `preflight-change`;
- `remote-preflight`;
- `finalize-workstream`;
- `review-reference-quality`.

Project-specific Skills are justified only for recurring domain workflows that carry non-obvious procedure or hazards.

## Definition of Done

A meaningful change progresses through applicable levels:

`CODE COMPLETE -> INTEGRATION COMPLETE -> FAILURE COMPLETE -> RESOURCE COMPLETE -> OPERATIONS COMPLETE -> EXPERIENCE COMPLETE -> OBSERVABILITY COMPLETE -> AUTOMATED PREFLIGHT COMPLETE -> EVIDENCE COMPLETE -> PRODUCT COMPLETE`

Not every change needs every level, but no applicable level should be silently skipped.

`OPERATIONS COMPLETE` means applicable canonical commands, E2E boundary/environment fidelity, build/artifact identity, build delta, runtime shutdown and ephemeral cleanup agree with the behavior being claimed.

`EXPERIENCE COMPLETE` applies when a user-facing interaction changes and means task model, information hierarchy, states/feedback, accessibility, adaptive layout, design-system/brand consistency and required UX/E2E/regression evidence agree with the claim being made.

`AUTOMATED PREFLIGHT COMPLETE` means the exact current head has no unresolved material ambiguity, the complete diff and intended target-base relationship were reviewed, and every required deterministic automatable gate for the blast radius passed through `AGENT_LOCAL`, `REMOTE_AUTOMATED`, or both at the required declared E2E fidelity. `REAL_ENVIRONMENT` evidence may remain explicitly pending, but absence of required automated evidence is never treated as a pass.

A change is not complete merely because code exists. The owning tests, integration/E2E behavior, failure/resource semantics, operational lifecycle, applicable experience semantics, documentation and evidence must agree with the claim being made.

## Branch and delivery policy

Projects should define a canonical integration/stable path appropriate to their release model. Protect canonical branches, require pull requests and required checks, prevent force pushes/deletion except explicit administration, and keep feature branches focused and short-lived.

Before automated readiness, verify the feature head against the current intended target base. If the target base moved after evidence was collected, refresh/reconcile the branch as appropriate to the repository's branching model and rerun invalidated gates. Stacked branches are conditional evidence until dependencies land and the stack is replayed or otherwise proven against the canonical base.

Release workflows should promote already-identified/validated artifacts rather than silently rebuilding or mutating an existing build identity unless the release process explicitly treats the rebuild as a new build.

## Adoption philosophy

For a new project, copy the smallest applicable core and selected profiles, then specialize all project-specific placeholders including `.engineering/commands.json` and `.engineering/e2e.json`. UI products should add `product-ui` only when a material user-facing interface exists and then specialize the design contracts rather than leaving generic placeholders.

For an existing project, audit before copying. Preserve good existing practices, native build/test tooling, design systems and stronger local mechanisms; identify conflicts and gaps and migrate incrementally. Never overwrite project-specific architecture, CI, command tooling, E2E framework/environment strategy, brand/design source or documentation blindly.

A project is self-contained after adoption. Template updates are explicit migrations, not runtime dependencies.
