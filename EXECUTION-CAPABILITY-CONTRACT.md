# Validation Execution Capability Contract

Version: 0.1.0

This contract defines **who executes required validation** when a repository is maintained by different kinds of coding agents. It complements `STANDARD.md` and `OPERATING-CONTRACT.md` without changing the strength of the required tests.

The governing rule is:

> Automation should execute automatable work. A human must not become the fallback test runner merely because a coding agent lacks a local shell, checkout, SDK or build environment.

A deterministic gate remains required even when the current agent cannot execute it locally. The execution location changes; the evidence requirement does not.

## 1. Execution classes

Every required validation gate is assigned, for the current agent/session, to exactly one execution class.

### `AGENT_LOCAL`

The current coding agent can execute the gate directly in its supported working environment against the exact current head.

Examples:

- formatter/static checks in an attached worktree;
- unit tests through an available shell;
- Gradle/Xcode/package-manager builds when the agent has the required SDK/toolchain;
- repository verifier scripts.

When an equivalent local environment exists, use it before remote confirmation. Remote CI should not replace a faster, equivalent agent-local edit/test loop.

### `REMOTE_AUTOMATED`

The gate is deterministic and automatable, but the current agent cannot execute it locally because its environment lacks the required checkout, shell, SDK, toolchain, service or compute capability.

Examples:

- Android Gradle/R8 validation from a ChatGPT Project operating through a GitHub connector;
- platform-specific compile/package jobs unavailable in the agent sandbox;
- deterministic integration tests that require a repository-owned remote runner.

These gates must be delegated to repository-owned automation, not to the user. CI may be the execution backend rather than merely a confirmation backend when the current agent lacks equivalent local execution capability.

### `REAL_ENVIRONMENT`

The evidence genuinely depends on a representative environment, physical hardware, external authority, protected credential, or human judgement that automation cannot truthfully substitute.

Examples:

- physical-device thermal/memory behavior;
- real hardware/driver compatibility;
- TalkBack/VoiceOver or representative usability evidence when manual evidence is required;
- an external production-like dependency that cannot safely be reproduced automatically;
- owner-only signing/release approval when the credential must not be available to automation.

`REAL_ENVIRONMENT` evidence may require the user or an authorized device/lab operator. It must never be used as a bucket for ordinary compile/test/build work that is merely inconvenient for the current agent.

## 2. No-human-runner principle

An automatable deterministic gate MUST NOT be delegated to the user solely because the coding agent lacks local execution capability.

Incorrect:

```text
agent has no Android SDK -> ask user to run ./gradlew check
```

Correct:

```text
agent has no Android SDK
-> classify Gradle/R8 gate as REMOTE_AUTOMATED
-> trigger repository-owned remote preflight
-> inspect result/logs
-> fix owning cause
-> retrigger automation
```

A user may explicitly choose a manual workaround, but repository procedure must not make that the normal path.

## 3. Agent-triggerable remote preflight

Repositories expected to be maintained by execution-limited agents should expose an agent-triggerable remote preflight mechanism.

The trigger may be a trusted PR comment such as `/preflight`, a dispatch API, a checked-in bot command or another repository-owned automation surface. The exact mechanism is project-owned and declared in `.engineering/commands.json`.

A remote preflight must:

- resolve the exact pull-request/head revision to validate;
- execute the same project-owned deterministic semantics used by normal CI/local tooling rather than inventing a second test policy;
- report PASS/FAIL and enough identity/log location for the agent to diagnose failures;
- be safely retriggerable after a fix;
- keep failure artifacts bounded and privacy-safe;
- avoid production secrets for untrusted or change-controlled code execution.

## 4. Security model for PR-triggered remote execution

When a PR comment or equivalent trigger executes code from a change branch:

- accept commands only from trusted repository associations/actors;
- resolve and pin the exact PR head SHA;
- default to same-repository PR heads unless fork execution is deliberately secured;
- run change-branch code with read-only or no repository credentials;
- do not expose production/signing/deployment secrets to the execution job;
- if a result must be written back to the PR, use a separate reporting job that does not execute or source change-branch code;
- preserve bounded timeout, artifact retention and concurrency controls.

A convenient remote runner is not allowed to weaken the repository trust boundary.

## 5. Readiness states

`preflight-change` reports one of these automation-readiness states:

- `READY_FOR_CI` — the current agent has equivalent local execution capability and every required `AGENT_LOCAL` deterministic gate passed; CI can independently confirm;
- `READY_FOR_REMOTE_PREFLIGHT` — semantic/base/diff checks and all available `AGENT_LOCAL` gates passed, while one or more required deterministic gates are `REMOTE_AUTOMATED`; the agent should trigger remote preflight rather than ask the user to run them;
- `AUTOMATED_PREFLIGHT_CONFIRMED` — every required deterministic automated gate for the exact head/base passed, whether executed locally, remotely, or both;
- `NOT_READY_FOR_AUTOMATED_PREFLIGHT` — material ambiguity, base/diff uncertainty, failed agent-local gate, missing required automation routing, or another blocker prevents truthful automated validation.

Real-environment evidence is tracked separately and may remain `PENDING` after automated preflight. It still blocks any stronger product/release claim that depends on it.

## 6. Failure loop

For a failing `REMOTE_AUTOMATED` gate:

```text
remote failure
-> inspect logs/evidence
-> classify failure
-> identify violated invariant + owner
-> patch owning cause
-> review diff/base impact
-> retrigger remote preflight
```

Do not ask the user to rerun the same automatable command between iterations. Do not repeatedly patch symptoms without a new falsifiable hypothesis.

## 7. Capability gaps are repository defects

If a required deterministic gate is automatable but the current agent cannot run it locally **and** the repository exposes no usable remote execution path, classify the situation as an automation-capability gap.

The preferred fix is to add or repair agent-triggerable remote automation. The fallback is not to permanently assign the command to a human.
