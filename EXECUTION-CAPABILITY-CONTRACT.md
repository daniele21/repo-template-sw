# Validation Execution Capability Contract

Version: 0.2.0

This contract defines **who executes required validation** and **how much automated validation is justified** when a repository is maintained by different kinds of coding agents. It complements `STANDARD.md` and `OPERATING-CONTRACT.md` without weakening required evidence.

The governing rules are:

> Automation should execute automatable work. A human must not become the fallback test runner merely because a coding agent lacks a local shell, checkout, SDK or build environment.

> Validation depth follows blast radius. Do not run a full repository/release matrix when a narrower automated profile can prove the changed invariants.

A deterministic gate remains required even when the current agent cannot execute it locally. The execution location changes; the evidence requirement does not. Likewise, remote execution does not imply running every possible gate.

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

## 2. Validation depth profiles

Execution location and validation depth are separate decisions.

The repository should expose an `auto` selector that maps the exact change blast radius to the narrowest sufficient profile. Recommended profiles are:

### `LEAN`

Use for changes with no executable/product/runtime blast radius, or for cheap universal guards that should always run.

Typical evidence:

- repository/governance verifier scripts;
- changed-file policy checks;
- syntax/config validation;
- lightweight formatter/linter checks where cheap;
- documentation/link/schema checks when applicable.

A docs-only PR should not initialize a full Android SDK, NDK, native toolchain or release build merely because those jobs exist elsewhere.

### `SCOPED`

Use for implementation changes whose blast radius is contained to one or a small number of owners/modules and their direct consumers.

Typical evidence:

- formatter/static analysis;
- affected module compilation;
- focused unit/component tests;
- affected direct-consumer/contract tests;
- affected-module lint;
- cheap compile checks for known integration consumers.

Do not build unrelated modules merely for completeness theater.

### `STRONG`

Use for cross-boundary or release-sensitive changes where a narrow module test cannot prove safety.

Typical escalation signals:

- public/shared API or protocol changes;
- persistence/migration/security/trust-boundary changes;
- native/JNI changes;
- Android manifest, packaging, R8/ProGuard, dependency or variant behavior;
- changes affecting multiple dependency cones;
- runtime/resource/concurrency/lifecycle ownership;
- shared integration fixtures or consumer compatibility.

Typical evidence adds the relevant broader integration cone, release/R8/minification or packaging checks, native host tests, assembled test APKs/artifacts, or other cross-boundary gates actually implicated by the change.

`STRONG` does not automatically mean every repository test. It means all evidence necessary for the material blast radius.

### `FULL`

Use when the selector itself cannot safely narrow the blast radius or when repository/release policy requires complete validation.

Typical triggers:

- promotion PRs to the stable/release branch;
- release-candidate validation;
- changes to CI scope detection, global build logic, dependency graph/inventory, toolchain/SDK/NDK configuration, or other machinery that decides what normally gets skipped;
- unknown/unclassified executable paths;
- intentionally requested full validation;
- periodic/nightly/reference-grade health runs where justified.

`FULL` should be exceptional on ordinary feature PRs, not the default.

## 3. Automatic profile selection

Profile selection should be deterministic and project-owned, normally from the changed paths plus repository dependency/ownership knowledge.

The default remote trigger should request `auto`, not `full`.

The selector should:

- identify changed paths against the exact intended base/head;
- treat docs-only/metadata-only changes cheaply when safe;
- map implementation files to owning modules/components;
- expand to direct consumers or dependency cones when contracts cross boundaries;
- escalate native/build/package/security/persistence/global configuration changes appropriately;
- fail safe to a stronger profile when an executable path is unknown;
- force `FULL` when the selector/build inventory itself changes and therefore cannot be trusted to narrow its own validation;
- report the chosen profile, reason and affected modules/jobs in the run summary.

Automatic **escalation** is allowed whenever additional risk is detected. Automatic silent **downgrade** below the project selector is not.

A human/agent may explicitly request a stronger profile (`strong`/`full`). A weaker-than-auto override should be exceptional, justified in the PR evidence, and should not be available as a casual way to skip required gates.

## 4. No-human-runner principle

An automatable deterministic gate MUST NOT be delegated to the user solely because the coding agent lacks local execution capability.

Incorrect:

```text
agent has no Android SDK -> ask user to run ./gradlew check
```

Correct:

```text
agent has no Android SDK
-> classify Gradle/R8 gate as REMOTE_AUTOMATED
-> select validation profile from blast radius
-> trigger repository-owned remote preflight with profile=auto
-> inspect result/logs
-> fix owning cause
-> retrigger automation
```

A user may explicitly choose a manual workaround, but repository procedure must not make that the normal path.

## 5. Agent-triggerable remote preflight

Repositories expected to be maintained by execution-limited agents should expose an agent-triggerable remote preflight mechanism.

The trigger may be a trusted PR comment such as `/preflight`, `/preflight strong` or `/preflight full`, a dispatch API, a checked-in bot command or another repository-owned automation surface. The exact mechanism is project-owned and declared in `.engineering/commands.json`.

The default command should be equivalent to `/preflight auto`.

A remote preflight must:

- resolve the exact pull-request/head revision to validate;
- determine the validation profile from blast radius unless a stronger profile was explicitly requested;
- execute the same project-owned deterministic semantics used by normal CI/local tooling rather than inventing a second test policy;
- report chosen profile, reason, affected modules/jobs, PASS/FAIL and enough identity/log location for the agent to diagnose failures;
- be safely retriggerable after a fix;
- keep failure artifacts bounded and privacy-safe;
- avoid production secrets for untrusted or change-controlled code execution.

## 6. Security model for PR-triggered remote execution

When a PR comment or equivalent trigger executes code from a change branch:

- accept commands only from trusted repository associations/actors;
- resolve and pin the exact PR head SHA;
- default to same-repository PR heads unless fork execution is deliberately secured;
- run change-branch code with read-only or no repository credentials;
- do not expose production/signing/deployment secrets to the execution job;
- if a result must be written back to the PR, use a separate reporting job that does not execute or source change-branch code;
- preserve bounded timeout, artifact retention and concurrency controls.

A convenient remote runner is not allowed to weaken the repository trust boundary.

## 7. Readiness states

`preflight-change` reports one of these automation-readiness states:

- `READY_FOR_CI` — the current agent has equivalent local execution capability and every required `AGENT_LOCAL` deterministic gate for the selected blast-radius profile passed; CI can independently confirm;
- `READY_FOR_REMOTE_PREFLIGHT` — semantic/base/diff checks and all available `AGENT_LOCAL` gates passed, while one or more required gates in the selected profile are `REMOTE_AUTOMATED`; the agent should trigger remote preflight rather than ask the user to run them;
- `AUTOMATED_PREFLIGHT_CONFIRMED` — every required deterministic automated gate in the selected profile for the exact head/base passed, whether executed locally, remotely, or both;
- `NOT_READY_FOR_AUTOMATED_PREFLIGHT` — material ambiguity, base/diff uncertainty, failed agent-local gate, missing required automation routing, invalid profile selection, or another blocker prevents truthful automated validation.

Real-environment evidence is tracked separately and may remain `PENDING` after automated preflight. It still blocks any stronger product/release claim that depends on it.

## 8. Failure loop

For a failing `REMOTE_AUTOMATED` gate:

```text
remote failure
-> inspect logs/evidence
-> classify failure
-> identify violated invariant + owner
-> patch owning cause
-> re-evaluate blast radius/profile
-> review diff/base impact
-> retrigger remote preflight
```

A fix can change the validation profile. For example, a local Kotlin fix may remain `SCOPED`, while adding a ProGuard rule or global Gradle change can legitimately escalate the next run to `STRONG` or `FULL`.

Do not ask the user to rerun the same automatable command between iterations. Do not repeatedly patch symptoms without a new falsifiable hypothesis.

## 9. Capability or scope gaps are repository defects

If a required deterministic gate is automatable but the current agent cannot run it locally **and** the repository exposes no usable remote execution path, classify the situation as an `AUTOMATION_CAPABILITY_GAP`.

If the repository cannot reliably decide what is affected, classify the situation as a `VALIDATION_SCOPE_GAP` and fail safe to a stronger profile while improving the selector.

The preferred fix is to add or repair agent-triggerable remote automation and blast-radius detection. The fallback is not to permanently assign the command to a human or run the entire repository on every ordinary PR forever.
