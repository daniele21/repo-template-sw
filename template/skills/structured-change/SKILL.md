---
name: structured-change
description: Guard meaningful code and product changes against duplicated ownership, unresolved material assumptions, unnecessary complexity, unbounded resources, incomplete failure handling, unsafe data lifecycle changes, operational residue, weak UX hierarchy and cross-layer contract drift.
---

# Structured Change

Use this Skill as a proportional pre-edit/pre-final review for meaningful behavior, architecture, persistence, API, concurrency, resource, security, build/runtime, user-facing experience or cross-layer changes.

It does **not** make every edit publication-ready. During `ITERATION`, keep reasoning proportional and use `validate-change` for fast evidence. Exact-head/full-diff/documentation preflight starts when a coherent outcome moves to `INTEGRATION` or `RELEASE`.

## 1. Find the owner before editing

- Locate the canonical contract/config/state owner.
- Inspect direct consumers, fakes and tests before changing a shared boundary.
- Search before adding constants, settings, statuses, endpoint paths, mappings or lifecycle rules.
- Prefer extending the existing owner over introducing parallel state or duplicated policy.
- For `product-ui`, find the canonical design/component/token owner before adding a visual pattern.

Ask: **who owns this after my change?** If there is no clear answer, fix ownership before adding behavior.

## 2. Resolve material ambiguity before implementation

Inspect canonical owners, durable docs/ADRs, direct consumers/fakes and tests first.

Ask the user only when unresolved alternatives would materially change product behavior, public/API/protocol contracts, persistence/migration, security/trust/privacy, lifecycle/resource semantics, compatibility, acceptance criteria or meaningful UX.

Do not ask about implementation-local naming/formatting/equivalent choices that preserve observable semantics.

## 3. Spend complexity deliberately

For every new abstraction, dependency, cache, service, worker, queue, layer, UI component or interaction pattern ask:

- Which concrete problem requires it?
- Why is the current boundary insufficient?
- What failure/resource/upgrade/experience surface does it add?
- Can a smaller direct solution preserve the same invariants?

Do not add speculative extensibility or visual novelty without a product reason.

## 4. Keep the outcome vertical

A meaningful change should preferably unlock an observable user/system outcome.

Technical layers are subtasks unless independently mergeable/value-bearing/reviewable. Parallel branches may own separate subtasks, but related work should converge early rather than creating a long publication stack.

Do not confuse “small technical layer” with “small vertical slice.”

## 5. Respect the project operating contract

Read `.engineering/commands.json` when the change affects setup, local runtime, validation, build, packaging, artifacts or cleanup.

Preserve applicable semantics:

- material builds receive unique identity;
- successful artifacts are immutable and promoted only after validation;
- comparable builds generate build delta;
- retention remains bounded;
- local runtimes close processes/listeners on every exit path;
- temporary resources are owned, isolated and cleaned deterministically.

Do not introduce a second undocumented run/build/package path when the canonical contract already owns that intent.

## 6. Respect the product experience contract

When `product-ui` is adopted, use `design-product-experience` at proportional depth for meaningful UI changes.

Check that the change:

- starts from user outcome/task rather than visual treatment;
- preserves or explicitly changes the owning IA/critical journey;
- preserves clear action hierarchy and progressive disclosure;
- uses sensible defaults;
- handles applicable loading/empty/error/disabled/offline/permission/partial states;
- gives timely actionable feedback/recovery;
- preserves accessibility/adaptive/platform behavior;
- reuses semantic tokens/components;
- uses motion only for a product purpose.

Do not use animation/illustration/polish to compensate for unresolved task flow or hierarchy.

## 7. Define resource lifecycle when applicable

For each significant resource identify owner/acquisition, lifetime/cardinality, budgets, concurrency/backpressure, timeout/cancellation, release/failure cleanup, idle/pressure behavior and metrics.

No unbounded queue/list/cache on an unbounded input path.

Temporary processes, sockets, locks, stores, workspaces, logs and caches are resources too. Cleanup covers success, failure, timeout, cancellation, interrupt and partial initialization.

## 8. Treat failure as normal behavior

Check applicable paths:

- invalid input;
- partial initialization/dependency failure;
- timeout/cancellation/shutdown;
- restart/recovery;
- interrupted persistence/migration;
- failed build/package promotion;
- stale PID/lock/temp state;
- user-facing loading/empty/error/offline/permission/partial states.

Cleanup/recovery must restore useful invariants rather than merely catch an exception or show a generic error.

When validation fails, classify the failure and owner before editing production code. Do not chase green checks with repeated symptom patches.

## 9. Preserve data/security semantics

When data changes, check creation, storage, owner, trust/encryption boundary, retention, deletion, backup/export, logging, migration and recovery.

Never introduce silent cloud fallback, content logging, secret persistence or destructive migration behavior without explicit contract/review. E2E/media evidence must remain privacy-safe.

## 10. Verify cross-layer contracts

When changing a public/shared boundary, inspect every material adapter/consumer and test. Keep domain policy out of UI/transport/persistence adapters unless that layer genuinely owns it.

UI text/controls should translate domain capability into the user's task rather than mirror internal service names mechanically.

## 11. Pre-final questions

- Is there one owner/source of truth?
- Are material ambiguities resolved?
- Is complexity proportional?
- Is this part of a coherent observable vertical outcome?
- Are resource bounds/cleanup and failure states coherent?
- Are build/artifact/runtime invariants preserved when applicable?
- If user-facing, are hierarchy/progressive disclosure/defaults/states/recovery/accessibility/adaptive behavior coherent?
- Are persisted data/trust boundaries preserved?
- Are tests/evidence focused on real invariants instead of implementation trivia?
- Is validation placed at the right delivery stage?
- Can deterministic prose rules move into tooling/CI?

A change is implementation-complete when applicable code, integration, failure/resource semantics, operating behavior and focused validation agree.

When the coherent outcome is ready to integrate, update affected durable documentation and use `preflight-change` to establish exact-head integration readiness. Do not perform that ceremony after every private edit.
