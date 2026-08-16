---
name: structured-change
description: Guard meaningful code changes against duplicated ownership, unnecessary complexity, unbounded resources, incomplete failure handling, unsafe data lifecycle changes and cross-layer contract drift.
---

# Structured Change

Use this Skill as a pre-edit and pre-final review for meaningful behavior, architecture, persistence, API, concurrency, resource, security or cross-layer changes.

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

## 3. Define resource lifecycle when applicable

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

## 4. Treat failure as normal behavior

Check applicable paths:

- invalid input;
- partial initialization;
- dependency failure;
- timeout;
- cancellation;
- shutdown during work;
- restart/recovery;
- interrupted persistence/migration.

Cleanup must restore ownership/invariants rather than merely catch an exception.

## 5. Preserve data/security semantics

When data changes, check creation, storage, owner, trust/encryption boundary, retention, deletion, backup/export, logging, migration and recovery.

Never introduce silent cloud fallback, content logging, secret persistence or destructive migration behavior without explicit contract/review.

## 6. Verify cross-layer contracts

When changing a public/shared boundary, inspect every material adapter/consumer and test. Keep domain policy out of UI/transport/persistence adapters unless that layer genuinely owns the policy.

## 7. Pre-final questions

- Is there exactly one owner/source of truth?
- Did the change add more complexity than the problem requires?
- Are resource bounds and cleanup explicit?
- Are cancellation/shutdown/failure states coherent?
- Are persisted/user data and trust boundaries preserved?
- Are tests focused on invariants rather than implementation trivia?
- Did documentation update only durable current truth?
- Can deterministic parts of this rule move from prose into tooling/CI?

A change is complete only when applicable code, integration, failure/resource semantics, validation and durable documentation agree.
