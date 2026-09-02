---
name: remote-preflight
description: Satisfy integration/release deterministic gates through repository-owned remote automation when local execution is unavailable, reusing equivalent successful evidence first and executing only missing, stale or insufficient gates.
---

# Remote Preflight

Use this Skill when `preflight-change` reaches `INTEGRATION` or `RELEASE` and one or more required deterministic gates are `REMOTE_AUTOMATED`.

The governing rules are:

> Do not turn the user into a CI runner because the current agent lacks tooling.

> Do not turn every integration slice into a full repository/release build.

> Before triggering a new expensive run, reuse successful equivalent evidence when it still proves the required claim.

## 1. Confirm required gates

Read `.engineering/commands.json` and record:

- stage: `INTEGRATION` or `RELEASE`;
- exact head and intended target/base;
- resolved risks, required gates and validation profile;
- selected E2E journey/environment/fidelity/evidence mode when applicable;
- remote trigger mechanism and security constraints.

If required deterministic work has no usable automation path, report `AUTOMATION_CAPABILITY_GAP`.

If the repository cannot safely narrow risk/gates, report `VALIDATION_SCOPE_GAP` and fail safe stronger while repairing the selector.

## 2. Search for reusable evidence

Before dispatching anything, inspect successful validation already associated with the candidate.

Evidence is reusable when it remains sufficient for:

- exact source head;
- material target/base relationship;
- required gate identity;
- selected profile or stronger equivalent profile;
- selected E2E environment/fidelity/evidence mode where applicable.

PR identity is not part of the proof by itself. A replacement PR using the same head/base/gates does not require an expensive rerun solely because its number changed.

Draft/ready transitions, labels, comments and other collaboration metadata do not invalidate source evidence.

Do not reuse evidence after a material head/base/dependency change or when the previous run did not include the currently required gates.

Record every reused run/gate explicitly.

## 3. Resolve only missing work

After evidence reuse, determine the remaining unsatisfied remote gates.

If none remain, return `AUTOMATED_PREFLIGHT_CONFIRMED` without starting another execution workflow.

Otherwise trigger the narrowest repository-owned automation capable of satisfying the missing gates. Default to the project `auto` selector unless a stronger profile is required.

Do not request `full` merely because it is operationally simpler.

## 4. Trigger exact-head automation

For every new run:

- pin the exact current head;
- preserve intended base identity;
- request only the necessary profile/gates;
- correlate result identity with the candidate;
- verify the reported selected risks/profile/gates match expectation.

A remote execution backend may orchestrate environment setup and caching, but deterministic semantics must remain project-owned rather than duplicated in workflow YAML.

## 5. Inspect results and evidence

For each required gate record `PASS`, `FAIL`, `PENDING` or `N/A`.

For E2E also record:

- journey;
- execution environment;
- fidelity class;
- selected UI evidence mode;
- required evidence artifacts for that mode.

`ASSERTIONS` does not require media merely because a UI process existed. `SCREENSHOTS` requires the selected checkpoints. `FULL_MEDIA` requires screenshots plus continuous journey video.

Missing evidence required by the selected mode is `E2E_EVIDENCE_INCOMPLETE`.

## 6. Repair autonomously

On failure:

1. inspect the failing job/step/log;
2. classify `CHANGE_REGRESSION`, `BASELINE_FAILURE`, `ENVIRONMENT`, `FLAKY`, `BASE_DRIFT` or `ASSUMPTION`;
3. identify the violated invariant and owner;
4. patch the owning cause when unambiguous;
5. re-evaluate risks/gates/profile because the repair may change scope;
6. invalidate only affected evidence;
7. reuse still-valid evidence and rerun only what remains necessary.

Do not ask the user to execute the same automatable test between repair attempts.

If the same gate fails after a repair, form a new falsifiable hypothesis before another patch.

## 7. Validation economics feedback

Remote latency is an engineering signal.

When an expensive gate runs frequently, ask whether it:

- catches unique regressions at that stage;
- belongs earlier as a cheaper focused test;
- belongs later at integration/release rather than iteration;
- overlaps substantially with another gate;
- is being triggered because the selector maps risk too broadly.

Do not delete a real safety invariant for speed. Improve placement and scope.

## 8. Security requirements

Remote execution of change-branch code should use:

- trusted requesters;
- exact-head pinning;
- same-repository heads by default;
- no production/deployment/signing secrets in execution jobs;
- read-only/no write credentials while change code executes;
- separate reporting permission when needed;
- bounded timeout and artifact retention.

Evidence reuse must not weaken these trust boundaries.

## 9. Output

Report:

```text
STAGE: INTEGRATION|RELEASE
HEAD: <revision>
TARGET: <branch>@<revision>
RISKS: <dimensions>
VALIDATION_PROFILE: LEAN|SCOPED|STRONG|FULL
REQUIRED_GATES: <list>
REUSED_EVIDENCE:
  <gate>: <run/ref>|N/A
NEW_REMOTE_GATES:
  <gate>: PASS|FAIL|PENDING|N/A
E2E:
  <journey>: <environment>/<fidelity>/<ASSERTIONS|SCREENSHOTS|FULL_MEDIA> / PASS|FAIL|PENDING|N/A
FAILURE_CLASS: <class|N/A>
REAL_ENVIRONMENT:
  <gate>: PENDING|PASS|N/A
READINESS: AUTOMATED_PREFLIGHT_CONFIRMED|NOT_READY_FOR_AUTOMATED_PREFLIGHT
```

`AUTOMATED_PREFLIGHT_CONFIRMED` requires every required deterministic automated gate to be satisfied by valid current evidence; it does not require rerunning evidence that is already equivalent and sufficient.
