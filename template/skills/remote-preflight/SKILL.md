---
name: remote-preflight
description: Execute and close required deterministic validation through repository-owned remote automation when the current coding agent lacks an equivalent local execution environment, without delegating automatable test work to the user.
---

# Remote Preflight

Use this Skill when `preflight-change` classifies one or more required deterministic gates as `REMOTE_AUTOMATED`.

The governing rule is:

> Do not turn the user into a CI runner because the current agent lacks a shell, checkout, SDK or platform toolchain.

## 1. Confirm remote execution ownership

Read `.engineering/commands.json` and identify the repository-owned remote-preflight mechanism.

Confirm:

- trigger mechanism and exact command/event;
- intended target PR/head;
- which deterministic canonical commands/gates it executes;
- how results/logs are surfaced;
- timeout/retention behavior;
- trust/security restrictions.

If the repository has no usable remote path for a required automatable gate, report an `AUTOMATION_CAPABILITY_GAP`. Prefer adding/repairing automation rather than asking the user to run the command manually.

## 2. Trigger exact-head validation

Trigger the declared remote preflight against the exact current PR/head revision.

For a PR-comment trigger such as `/preflight`:

- verify the PR still targets the intended base;
- record the current head SHA before triggering;
- issue the exact declared command once;
- correlate the resulting run/report with that head SHA.

Do not reuse a remote result from an older head after any material edit, rebase, replay or base change.

## 3. Inspect result and logs

For each required remote gate, record `PASS`, `FAIL`, `PENDING` or `N/A`.

On failure:

1. inspect the failing job/step/log rather than guessing from the headline;
2. classify the failure as `CHANGE_REGRESSION`, `BASELINE_FAILURE`, `ENVIRONMENT`, `FLAKY`, `BASE_DRIFT` or `ASSUMPTION`;
3. identify the violated invariant and owning source/configuration;
4. determine whether the remote runner exposed a local/remote parity gap;
5. form a falsifiable repair hypothesis before editing.

A remote failure is not permission to suppress R8/lint/tests, add broad keep rules blindly, or weaken another legitimate gate.

## 4. Repair and retrigger autonomously

When the failure is actionable and unambiguous:

- patch the owning cause;
- run any available cheap `AGENT_LOCAL` checks/static review;
- refresh head/base identity and complete-diff review as needed;
- retrigger remote preflight;
- inspect the new exact-head result.

Do not ask the user to execute the same automatable test between repair attempts.

If the same gate fails after a repair, stop symptom patching and form a new root-cause hypothesis before the next edit. Escalate to the user only if a material product/contract decision becomes genuinely ambiguous or real-environment evidence is required.

## 5. Security requirements

For repository automation that executes PR/change-branch code, verify that the implementation follows the local security contract. Prefer:

- trusted requesters only;
- exact-head pinning;
- same-repository PRs by default;
- no production/deployment/signing secrets in the execution job;
- read-only/no write credentials while change-branch code executes;
- a separate reporting job if PR write permission is needed;
- bounded timeout and failure-artifact retention.

Do not solve an execution-capability problem by weakening the repository trust boundary.

## 6. Output

Report:

```text
HEAD: <revision>
TARGET: <branch>@<revision>
REMOTE_TRIGGER: <mechanism>
REMOTE_GATES:
  <gate>: PASS|FAIL|PENDING|N/A
FAILURE_CLASS: <class|N/A>
REAL_ENVIRONMENT:
  <gate>: PENDING|PASS|N/A
READINESS: AUTOMATED_PREFLIGHT_CONFIRMED|NOT_READY_FOR_AUTOMATED_PREFLIGHT
```

`AUTOMATED_PREFLIGHT_CONFIRMED` requires every deterministic automatable gate required by the blast radius to pass on the exact current head/base. It does not imply physical-device, hardware, representative-user, signing or release evidence unless those gates also ran.
