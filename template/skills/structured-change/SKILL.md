---
name: structured-change
description: Guard meaningful code changes against duplicated ownership, unnecessary complexity, unbounded resources, incomplete failure handling, unsafe data lifecycle changes, operational residue and cross-layer contract drift.
---

# Structured Change

Use this Skill as a pre-edit and pre-final review for meaningful behavior, architecture, persistence, API, concurrency, resource, security, build/runtime or cross-layer changes.

## 1. Find the owner before editing

- Locate the canonical contract/config/state owner.
- Inspect direct consumers, fakes and tests before changing a shared boundary.
- Search before adding constants, settings, statuses, endpoint paths, mappings or lifecycle rules.
- Prefer extending the existing owner over introducing parallel state or duplicated policy.

Ask: **who owns this after my change?** If there is no clear answer, fix ownership before adding behavior.

## 2. Spend complexity deliberately

For every new abstraction, dependency, cache, service, worker, queue or layer ask:

- Which concrete problem requires it?
- Why is the current boundary insufficient?
- What failure/resource/upgrade surface does it add?
- Can a smaller direct solution preserve the same invariants?

Do not add speculative extensibility.

## 3. Respect the project operating contract

Read `.engineering/commands.json` when the change affects setup, local runtime, validation, build, packaging, artifacts or cleanup.

Preserve the common semantics even when the underlying tool changes:

- a material `build` receives a unique build identity;
- successful artifacts are immutable and promoted only after validation;
- every successful comparable build generates its build delta;
- local artifact retention remains bounded;
- project-owned local runtimes close processes/listeners on every exit path;
- temporary resources are owned, isolated and cleaned deterministically.

Do not introduce a second undocumented way to run/build/package the project when the canonical command contract already owns that intent.

## 4. Define resource lifecycle when applicable

For each new/changed significant resource identify:

- owner and acquisition;
- lifetime and maximum cardinality;
- memory/disk/CPU/GPU budget where material;
- concurrency/backpressure behavior;
- timeout/cancellation;
- release and failure cleanup;
- idle/pressure behavior;
- metrics/diagnostics.

No unbounded queue/list/cache on an unbounded input path. Prefer admission/reservation before expensive allocation when exhaustion is predictable.

Temporary processes, sockets, locks, test stores, workspaces, build staging areas, logs and caches are resources too. Cleanup must cover success, failure, timeout, cancellation, user interrupt and partial initialization.

## 5. Treat failure as normal behavior

Check applicable paths:

- invalid input;
- partial initialization;
- dependency failure;
- timeout;
- cancellation;
- shutdown during work;
- restart/recovery;
- interrupted persistence/migration;
- failed build/package promotion;
- stale PID/lock/temp state from a previous crashed run.

Cleanup must restore ownership/invariants rather than merely catch an exception. Never delete a shared/global resource unless project ownership is proven.

## 6. Preserve data/security semantics

When data changes, check creation, storage, owner, trust/encryption boundary, retention, deletion, backup/export, logging, migration and recovery.

Never introduce silent cloud fallback, content logging, secret persistence or destructive migration behavior without explicit contract/review. Temporary credentials/signing data must not leak into logs, caches or distributed artifacts.

## 7. Verify cross-layer contracts

When changing a public/shared boundary, inspect every material adapter/consumer and test. Keep domain policy out of UI/transport/persistence adapters unless that layer genuinely owns the policy.

## 8. Pre-final questions

- Is there exactly one owner/source of truth?
- Did the change add more complexity than the problem requires?
- Are resource bounds and cleanup explicit?
- Are cancellation/shutdown/failure states coherent?
- Does a local runtime leave no project-owned process/listener after stop?
- Are build/artifact identity, immutability, retention and build delta preserved when applicable?
- Are persisted/user data and trust boundaries preserved?
- Are tests focused on invariants rather than implementation trivia?
- Did documentation update only durable current truth?
- Can deterministic parts of this rule move from prose into tooling/CI?

A change is complete only when applicable code, integration, failure/resource semantics, operating-contract behavior, validation and durable documentation agree.
