# Using `repo-template-sw`

This guide explains how to use `repo-template-sw` with a brand-new repository, an existing repository, and an already-adopted repository that needs to move to a newer baseline.

`repo-template-sw` is a **bootstrap, audit and migration source**. It is not a runtime dependency and should not be consulted for every ordinary coding task after a project has adopted the baseline.

## Mental model

```text
repo-template-sw
      |
      +--> new repository      -> bootstrap and specialize
      |
      +--> existing repository -> audit, preserve, align
      |
      +--> adopted repository  -> explicit baseline migration

After adoption:

project repository
      |
      +--> AGENTS.md      -> routing and durable invariants
      +--> local Skills   -> recurring development workflows
      +--> active plan    -> only current coordinated work
      +--> durable docs   -> how the system works now
      +--> scripts / CI   -> deterministic enforcement
```

The goal is not to make repositories look identical. The goal is to make them converge on a common set of engineering invariants while remaining self-contained and adapted to their stack and product.

---

# 1. Starting a repository from zero

Use this path when the product repository is new or contains no meaningful engineering structure yet.

## Recommended request to a coding agent

```text
Bootstrap <TARGET_REPOSITORY> using daniele21/repo-template-sw at the current stable baseline.

Use the adopt-engineering-standard workflow.

Before implementing product features:
1. identify the product/runtime, languages, platforms, persistence, network and security boundaries;
2. select only the applicable profiles from repo-template-sw;
3. adopt and specialize the universal baseline;
4. create a project-specific AGENTS.md with real ownership, routing and validation guidance;
5. configure stack-specific formatting, linting, tests, build and CI gates;
6. record the adopted standard version and selected profiles in .engineering/baseline.json;
7. run repository, documentation and agent-context validation;
8. report the resulting maturity level truthfully.

Do not leave generic placeholders. Do not add stack profiles or abstractions that the project does not need.
```

## What the agent should take from the template

The universal baseline under `template/` provides the starting structure for:

- `AGENTS.md`;
- `CONTRIBUTING.md`;
- `SECURITY.md`;
- `.engineering/baseline.json`;
- `.engineering/documentation-policy.json`;
- architecture, current-state, feature, ADR and workstream documentation;
- the five project-local core Skills;
- repository/documentation/agent-context health checks;
- a baseline pull-request template and health workflow.

The agent must **specialize** these files. A copied generic `AGENTS.md` is not a completed adoption.

## Select profiles deliberately

Profiles under `profiles/` are optional deltas, not packages that must all be installed.

Examples:

```text
Python local inference service
-> core + python + local-ai

Android application
-> core + android

Android application running local AI
-> core + android + local-ai

macOS Python application with local models
-> core + python + macos + local-ai

TypeScript web application
-> core + typescript
```

Only adopt requirements that protect a real invariant for the target project.

## Expected result

A new project should end bootstrap with a repository similar in responsibilities to:

```text
project/
├── AGENTS.md
├── README.md
├── CONTRIBUTING.md
├── SECURITY.md
├── .engineering/
│   ├── baseline.json
│   └── documentation-policy.json
├── docs/
│   ├── README.md
│   ├── architecture.md
│   ├── current-state.md
│   ├── adr/
│   ├── features/
│   └── workstreams/
├── skills/
│   ├── plan-workstream/
│   ├── structured-change/
│   ├── validate-change/
│   ├── finalize-workstream/
│   └── review-reference-quality/
├── scripts/
│   ├── verify_repository.py
│   ├── verify_docs.py
│   └── verify_agent_context.py
└── project-specific source, tests and CI
```

The exact folder layout may differ when a stronger project-specific structure already exists or the stack requires something else.

## Important maturity rule

Copying the baseline does **not** make a repository L1 or L2.

Bootstrap establishes structure and engineering expectations. Production or reference-grade maturity requires real implementation and evidence: tests, failure behavior, resource bounds, security controls, observability, performance evidence and other applicable gates from `STANDARD.md`.

---

# 2. Aligning an existing repository

Use this path when the project already has code, tests, documentation, CI or its own agent instructions.

**Do not copy `template/` over the repository.** Existing repositories must be audited first.

## Recommended first request: audit only

```text
Audit <TARGET_REPOSITORY> against the current stable baseline of daniele21/repo-template-sw.

Use adopt-engineering-standard, but do not modify the repository yet.

For every relevant baseline concern classify the current project as:
- KEEP: existing mechanism is equal or stronger;
- ADAPT: good mechanism that should be aligned without losing local value;
- ADD: meaningful gap;
- N/A: not applicable;
- CONFLICT: existing behavior contradicts a required invariant.

Return:
- current estimated maturity: L0 / L1 / L2;
- architecture and ownership gaps;
- memory/resource/concurrency/failure gaps where applicable;
- security and data-lifecycle gaps;
- CI/testing/reproducibility gaps;
- AGENTS.md and coding-agent context quality;
- documentation entropy and duplicated/obsolete plans;
- repository hygiene issues;
- what should explicitly NOT be changed;
- a small dependency-aware adoption DAG;
- tasks that can safely run in parallel.
```

This audit is the most important step. It prevents the template from replacing stronger practices merely for visual consistency.

## Recommended second request: implement the adoption

After reviewing the audit:

```text
Implement the approved repo-template-sw adoption plan for <TARGET_REPOSITORY>.

Preserve all items classified KEEP.
Adapt rather than overwrite project-specific AGENTS.md, CI, architecture and Skills.
Add only justified baseline gaps.
Keep the adoption workstream bounded and parallelize independent tasks where safe.
Run project-specific validation plus the adopted repository health checks.
At completion, transfer durable knowledge to the appropriate current docs and delete the temporary adoption plan unless it has independent audit value.
```

## Typical adoption decisions

Examples:

```text
Existing strong test suite
-> KEEP

Large but useful AGENTS.md with too much domain detail
-> ADAPT into root routing + scoped guides

No SECURITY.md
-> ADD

Existing architecture document stronger than template placeholder
-> KEEP and link it as canonical owner

Existing project-specific Skill that enforces stronger change discipline
-> KEEP or MERGE

Completed implementation plans still treated as current truth
-> ADAPT: transfer durable facts, then delete/archive only when justified

No resource lifecycle contract in a local-AI runtime
-> ADD
```

## Adoption is complete when

- the repository is self-contained;
- project-specific ownership and routing are explicit;
- copied placeholders are gone;
- stronger local practices were preserved;
- `.engineering/baseline.json` records the adopted version and profiles;
- relevant baseline checks run successfully;
- project-specific CI/test/build gates remain authoritative;
- temporary adoption planning has been finalized and removed by default.

---

# 3. Normal development after adoption

After adoption, **do not use `repo-template-sw` for every feature or bug fix**.

Normal work should be driven entirely from the target repository:

```text
user request
    |
    v
project AGENTS.md
    |
    +--> closest scoped AGENTS.md when needed
    |
    +--> project-local Skill when the workflow requires it
    |
    +--> focused code, tests and durable docs
```

A normal request should be small, for example:

```text
Implement memory-aware eviction.
Parallelize independent work where safe.
```

The repository's own `AGENTS.md` and Skills should provide the recurring engineering procedure. The user should not need to restate the whole standard in every prompt.

## When to create an active workstream

Use `plan-workstream` only when the change has meaningful coordination, dependencies, parallel lanes or multiple acceptance gates.

Do not create a plan for every small change.

A workstream should be a compact execution DAG, not a development diary.

```text
ID | scope | depends on | parallel | state | acceptance
```

At completion:

```text
plan
 -> implement
 -> validate
 -> transfer durable knowledge
 -> delete plan
```

Git remains the implementation history.

---

# 4. Updating a repository to a newer baseline

Use `repo-template-sw` again when the engineering standard itself changes materially.

Do not automatically sync every template commit into every project.

## Recommended request

```text
Migrate <TARGET_REPOSITORY> from its recorded repo-template-sw baseline to <TARGET_VERSION>.

Use update-engineering-standard.

Read the project's .engineering/baseline.json and compare its recorded version with repo-template-sw VERSION and CHANGELOG.
Classify every relevant delta as APPLY, MERGE, N/A, DEFER or CONFLICT.
Preserve local customizations and stronger project-specific mechanisms.
Do not replace customized Skills or AGENTS.md wholesale.
Implement only the relevant semantic changes, run affected validation, then update baseline metadata.
```

## Recommended migration policy

Projects do not need to stay on exactly the same baseline version at all times.

A healthy state may look like:

```text
project-a -> baseline 0.1.0
project-b -> baseline 0.2.0
project-c -> baseline 0.2.0 + customized structured-change Skill
```

Upgrade when the newer baseline contains relevant improvements, not merely because the template has a newer commit.

---

# 5. Using the standard as an audit tool

`repo-template-sw` can also be used without adoption or modification.

Recommended request:

```text
Review <TARGET_REPOSITORY> against daniele21/repo-template-sw.
Do not change code.

Return a repository health assessment across:
- architecture and ownership;
- complexity and dependencies;
- memory/resources and concurrency where applicable;
- failure handling;
- security/privacy/data lifecycle;
- observability;
- testing and reproducibility;
- performance evidence;
- repository hygiene;
- documentation lifecycle;
- coding-agent operability and estimated context cost.

Estimate current L0/L1/L2 maturity and rank the highest-value gaps.
```

This is useful before large refactors, releases or architecture hardening work.

---

# 6. Promoting lessons back into `repo-template-sw`

The flow is bidirectional:

```text
repo-template-sw -> projects
projects -> real-world lessons -> repo-template-sw
```

Do not promote every project-specific improvement into the universal baseline.

A good default filter is:

1. Is the practice genuinely cross-project rather than product-specific?
2. Does it protect a meaningful invariant or recurring workflow?
3. Has it proven useful in real implementation rather than only in theory?
4. Can it be expressed without forcing an unnecessary dependency or architecture?
5. Does adding it reduce future risk/context cost more than it increases template complexity?

Prefer evidence from at least two different project contexts for non-obvious practices before making them universal.

---

# 7. What belongs where

Use this rule when deciding whether information belongs in the template or a project:

| Concern | Canonical owner |
| --- | --- |
| Universal engineering invariant | `repo-template-sw/STANDARD.md` |
| Universal adoption/update procedure | `repo-template-sw/skills/` |
| Optional stack/domain baseline | `repo-template-sw/profiles/` |
| Project routing and invariants | project `AGENTS.md` |
| Recurring project development procedure | project `skills/` |
| Current coordinated implementation | project active workstream |
| Current system behavior | project feature/architecture docs |
| Durable architecture decision | project ADR |
| Implementation history | Git |
| Deterministic enforceable rule | scripts / CI |

A useful heuristic is:

> If a machine can enforce the rule deterministically, prefer code/CI over spending coding-agent tokens explaining it repeatedly.

---

# 8. Quick reference

## New repository

```text
repo-template-sw
 -> adopt-engineering-standard
 -> select profiles
 -> specialize template
 -> configure project validation
 -> record baseline
 -> start product work
```

## Existing repository

```text
repo-template-sw
 -> audit KEEP / ADAPT / ADD / N/A / CONFLICT
 -> review adoption DAG
 -> migrate incrementally
 -> validate
 -> finalize/delete adoption workstream
```

## Ordinary development

```text
project only
 -> AGENTS.md
 -> local Skill when needed
 -> code/tests
 -> validate
 -> durable docs only
```

## Baseline upgrade

```text
baseline.json
 -> VERSION + CHANGELOG comparison
 -> APPLY / MERGE / N/A / DEFER / CONFLICT
 -> focused migration
 -> validation
 -> baseline metadata update
```

The intended outcome is simple: **use `repo-template-sw` to establish and evolve the engineering system, then let each project operate independently inside that system.**
