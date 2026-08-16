# Agent-Native Reference Engineering Standard

Version: 0.1.0

## Purpose

This standard defines the minimum engineering properties expected from software repositories maintained by humans and coding agents. It optimizes for software correctness, operational simplicity, bounded resource use, change safety, reproducibility, and low context cost.

The central rule is:

> Make ownership, limits, failures and costs explicit, using the simplest solution that preserves the required invariants.

The standard is intentionally not a framework. A project should adopt only the mechanisms justified by its real requirements.

## Operating principles

### Simplicity is a budget

Every dependency, abstraction, cache, worker, service, queue, layer and document adds maintenance and reasoning cost. Add one only when it solves an observed or clearly specified problem. Prefer direct code over speculative extensibility. Redesign a bad boundary rather than stacking compensating abstractions over it.

### One owner for every important fact

Mutable state, public contracts, configuration values, persisted data, caches and significant resources must have an identifiable owner. Avoid parallel sources of truth, scattered globals and duplicated policy.

### Failure is part of the design

Success, invalid input, partial failure, timeout, cancellation, shutdown and cleanup are normal lifecycle paths. Critical components define and test each applicable path.

### Resources are contracts

For every significant resource, define owner, acquisition, lifetime, maximum cardinality, budget, concurrency, backpressure, timeout, cancellation, release, cleanup, idle/pressure behavior and observability. OOM is not a resource-management strategy.

### Machines enforce what machines can check

Do not spend agent context reminding a model about rules that deterministic tooling can enforce. Formatting, architecture boundaries, document budgets, token budgets, tests, generated-artifact bans and similar invariants belong in scripts and CI where practical.

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
- formatting, lint/static checks, tests and build validation appropriate to the stack;
- CI on pull requests and protected canonical branches;
- explicit configuration and no committed secrets;
- bounded resources, queues and caches where applicable;
- timeouts, cancellation and cleanup for long-lived operations;
- structured/error-classified logging without sensitive payloads by default;
- security/trust-boundary documentation;
- repository hygiene: no generated build output, large model/media artifacts or private local state unless explicitly justified;
- one current-state ledger and bounded active workstreams;
- no completed implementation plans kept as active documentation.

### L1 — Production ready

L0 plus:

- integration/contract or end-to-end tests for critical workflows;
- migration and backward-compatibility strategy where data/contracts persist;
- failure, cancellation and shutdown tests for critical lifecycle components;
- backup/restore or recovery procedures where user/business data requires them;
- performance and resource budgets for important paths;
- observability sufficient to answer what is running, why, how long, resource use and failure cause;
- release procedure, rollback strategy and operational runbooks;
- dependency/security scanning appropriate to the threat model;
- real environment evidence for behavior that cannot be truthfully validated in CI.

### L2 — Reference grade

L1 plus:

- architecture fitness functions for critical dependency/ownership invariants;
- resource and memory regression tests;
- performance regression gates for critical paths where stable measurement is possible;
- pressure/fault-injection coverage for important lifecycle boundaries;
- representative device/hardware evidence when hardware materially changes behavior;
- machine-enforced documentation and agent-context budgets;
- explicit complexity/dependency review for meaningful additions;
- reproducible benchmark/evidence identity where results influence engineering decisions;
- automated repository policy/health validation;
- stale/duplicate documentation and completed-work detection.

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

For every significant resource — model, tokenizer, KV cache, audio/video buffer, worker, thread, process, socket, HTTP client, database connection, temporary file, cache, job queue or similar — define as applicable:

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

## Concurrency and backpressure

Define maxima for requests, workers and expensive operations. Define what happens at capacity: queue, reject, wait, degrade or evict. Do not spawn indefinitely. Queue capacity and timeout semantics must be explicit and observable.

## Failure and recovery

Critical workflows should define behavior for success, invalid input, partial initialization, dependency failure, timeout, cancellation, process/app shutdown and restart/recovery where applicable.

Test failure at the owning boundary. Useful scenarios include partial load, corrupt input, disk full, interrupted write, process death, cancellation during work and shutdown during active work.

## Data lifecycle and security

For each meaningful data category define creation, storage location, owner, encryption/trust boundary, retention, deletion, backup, export, logging, migration and recovery.

Defaults should minimize exposure. Remote/network access must be explicit when local-only behavior is expected. Never silently fall back to cloud processing. Secrets, private user paths and sensitive content do not belong in source control or normal telemetry.

## Observability

Operational evidence should answer:

- what is the system doing;
- why is it doing it;
- which operation/request/job owns the work;
- how long has it taken;
- what resources are consumed;
- why did it fail or degrade.

Use truthful metric names, units and sources. Unavailable data remains unavailable rather than becoming zero. Keep correlation identifiers privacy-safe. For local AI systems, consider resident models, memory/VRAM or unified memory, queue depth, active jobs, load time, time-to-first-token, throughput, cache hits and eviction reason when applicable.

## Testing

Optimize for tested invariants rather than an arbitrary coverage percentage. Every critical invariant should have a deterministic test when technically possible.

Examples:

- an active resource cannot be evicted;
- cancellation releases ownership and reservations;
- unsupported combinations fail before backend execution;
- a proposed financial/import record is not canonical before required review;
- cloud processing is never implicit;
- backup/export round trips preserve required data;
- repeated lifecycle operations do not leak resident resources.

Use the narrowest useful test loop while iterating, then expand validation according to change scope.

## Performance

Important projects should define measurable budgets appropriate to their product: startup, idle and peak memory, latency/percentiles, throughput, shutdown, binary size, storage growth or queue wait. Measure before optimizing and avoid performance claims without representative evidence.

## Reproducibility

A clean checkout should have a documented path to setup, test and run. Pin toolchains where practical, commit lockfiles, validate configuration and avoid environment-specific hidden state. Benchmark/evidence artifacts used for decisions should include enough identity to be reproduced.

## Repository hygiene

Git should contain source, tests, configuration, small fixtures, durable documentation and small durable assets. Prefer release assets, artifact storage or LFS when justified for large binaries/media. Generated bundles, build output, model weights, logs, caches, private data and temporary evidence should not accumulate in normal source history.

## Documentation lifecycle

Use one canonical owner for each durable fact. Prefer links over duplicate detailed descriptions.

Recommended active documentation:

- `docs/architecture.md`: current boundaries and ownership;
- `docs/features/`: durable current behavior when additional explanation is useful;
- `docs/adr/`: durable architectural decisions and their rationale;
- `docs/current-state.md`: short volatile repository-level status;
- `docs/workstreams/`: only active, bounded implementation plans;
- runbooks/evidence docs only when the project requires them.

A completed workstream follows:

`plan -> implement -> validate -> transfer durable knowledge -> delete plan`

Do not create a document solely to record that a PR or isolated implementation step completed.

## Agent-operability

### Root guide

`AGENTS.md` is a routing layer, not a repository encyclopedia. It contains only durable repository-wide invariants, ownership/routing, task reading rules and validation selection.

### Scoped guides

Add a scoped `AGENTS.md` only when a subtree has meaningful local invariants, hazards, ownership or validation commands. The closest applicable guide should let an agent avoid loading unrelated domains.

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
- `validate-change`;
- `finalize-workstream`;
- `review-reference-quality`.

Project-specific Skills are justified only for recurring domain workflows that carry non-obvious procedure or hazards.

## Definition of Done

A meaningful change progresses through applicable levels:

`CODE COMPLETE -> INTEGRATION COMPLETE -> FAILURE COMPLETE -> RESOURCE COMPLETE -> OBSERVABILITY COMPLETE -> EVIDENCE COMPLETE -> PRODUCT COMPLETE`

Not every change needs every level, but no applicable level should be silently skipped.

A change is not complete merely because code exists. The owning tests, integration behavior, failure/resource semantics, documentation and evidence must agree with the claim being made.

## Branch and delivery policy

Projects should define a canonical integration/stable path appropriate to their release model. Protect canonical branches, require pull requests and required checks, prevent force pushes/deletion except explicit administration, and keep feature branches focused and short-lived.

## Adoption philosophy

For a new project, copy the smallest applicable core and selected profiles, then specialize all project-specific placeholders.

For an existing project, audit before copying. Preserve good existing practices, identify conflicts and gaps, and migrate incrementally. Never overwrite project-specific architecture, CI or documentation blindly.

A project is self-contained after adoption. Template updates are explicit migrations, not runtime dependencies.
