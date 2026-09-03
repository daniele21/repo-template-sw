---
name: remote-preflight
description: Satisfy integration/release deterministic gates through repository-owned remote automation, reusing equivalent successful evidence before executing only missing, stale or insufficient gates.
---

# Remote Preflight

Use this Skill when `preflight-change` reaches `INTEGRATION` or `RELEASE` and one or more required deterministic gates are `REMOTE_AUTOMATED`.

The governing rules are:

> Do not turn the user into a CI runner because the current agent lacks tooling.

> Do not turn every integration slice into a full repository/release build.

> Reuse successful evidence when source identity, target relationship and required claim are still equivalent.

## 1. Confirm required gates

Read `.engineering/commands.json` and record stage, exact head, intended target/base, source tree when available, risks, required gates, profile, applicable E2E environment/fidelity/evidence mode, remote trigger and security constraints.

If required deterministic work has no usable automation path, report `AUTOMATION_CAPABILITY_GAP`. If risk/gates cannot be narrowed safely, report `VALIDATION_SCOPE_GAP` and fail safe stronger while repairing the selector.

## 2. Search for reusable evidence

Before dispatching anything, inspect successful validation already associated with the candidate.

For an integration candidate before merge, evidence normally matches:

- exact source head;
- material target/base relationship;
- required gates;
- selected profile or stronger equivalent profile;
- selected E2E environment/fidelity/evidence mode where applicable.

Collaboration metadata is not evidence identity. Recreating a PR, changing draft/ready state, labels or comments does not require a rerun when the source proof is unchanged.

### Content-preserving post-merge reuse

A repository may also reuse successful integration evidence after a content-preserving merge transformation such as squash/rebase **only** when all of the following are true:

- the validated integration candidate had successful current evidence;
- the post-merge commit has the exact same Git source tree as the validated candidate;
- the push base is the same target/base revision against which that candidate was validated;
- required gates/profile and relevant E2E environment/evidence mode are equal or weaker than the validated proof;
- the repository-owned workflow controls the evidence identity and artifact lookup.

The post-merge commit SHA may differ because commit metadata/history changed; the source tree may not. Treat this as content-equivalent reuse, not as proof that the old run executed on the new commit object.

Never apply tree-equivalent reuse to `RELEASE` unless the release policy explicitly allows it. Never apply it when the target/base moved, the Git tree differs, gates broadened, evidence expired, or validation identity cannot be proven.

A direct push to an integration branch without matching trusted evidence must run the selected validation normally.

Record reused evidence and its identity explicitly.

## 3. Resolve only missing work

If every required deterministic gate is satisfied by valid evidence, return `AUTOMATED_PREFLIGHT_CONFIRMED` without starting another expensive run. Otherwise trigger only the narrowest repository-owned automation needed for missing gates, defaulting to the project `auto` selector.

Do not request `full` merely because it is simpler operationally.

## 4. Trigger new automation safely

For every new run, pin the exact current head, preserve target/base identity, request only necessary gates/profile, correlate the result with the candidate, and verify reported risks/profile/gates. Deterministic semantics remain project-owned rather than duplicated inconsistently in orchestration YAML.

## 5. Inspect results and evidence

For each gate record `PASS`, `FAIL`, `PENDING` or `N/A`. For E2E also record journey, environment, fidelity, selected UI evidence mode and required artifacts. Missing artifacts required by the selected mode are `E2E_EVIDENCE_INCOMPLETE`.

## 6. Repair autonomously

On failure: inspect the failing evidence; classify `CHANGE_REGRESSION`, `BASELINE_FAILURE`, `ENVIRONMENT`, `FLAKY`, `BASE_DRIFT` or `ASSUMPTION`; identify the owner; patch the owning cause; re-evaluate risks/gates/profile; invalidate only affected evidence; then reuse or rerun only what remains necessary.

Do not ask the user to execute the same automatable test between repair attempts. Repeated failure after a repair requires a new falsifiable hypothesis.

## 7. Validation economics

When an expensive gate runs frequently, assess whether it catches unique regressions at that stage, belongs earlier as a cheaper focused test, belongs later at integration/release, overlaps substantially with another gate, or is triggered by overly broad risk mapping. Improve placement/scope without deleting a real invariant.

## 8. Security

Remote execution of change-branch code should use trusted requesters, exact-head pinning for new runs, same-repository heads by default, no production/deployment/signing secrets, read-only execution credentials, separate reporting permission where needed, and bounded timeout/evidence retention.

Evidence reuse must not weaken these trust boundaries.

## 9. Output

Report:

```text
STAGE: INTEGRATION|RELEASE
HEAD: <revision>
SOURCE_TREE: <tree|N/A>
TARGET: <branch>@<revision>
RISKS: <dimensions>
VALIDATION_PROFILE: LEAN|SCOPED|STRONG|FULL
REQUIRED_GATES: <list>
REUSED_EVIDENCE:
  <gate>: <run/ref/identity>|N/A
NEW_REMOTE_GATES:
  <gate>: PASS|FAIL|PENDING|N/A
E2E:
  <journey>: <environment>/<fidelity>/<ASSERTIONS|SCREENSHOTS|FULL_MEDIA> / PASS|FAIL|PENDING|N/A
FAILURE_CLASS: <class|N/A>
REAL_ENVIRONMENT:
  <gate>: PENDING|PASS|N/A
READINESS: AUTOMATED_PREFLIGHT_CONFIRMED|NOT_READY_FOR_AUTOMATED_PREFLIGHT
```

`AUTOMATED_PREFLIGHT_CONFIRMED` requires every required deterministic automated gate to be satisfied by valid evidence; it never requires rerunning proof solely because collaboration metadata or a content-preserving integration commit changed commit identity.
