# Documentation map

Use progressive disclosure. Start from `AGENTS.md`, add the closest scoped agent guide, then read only the canonical source required by the task.

## Canonical owners

| Question | Source |
| --- | --- |
| What is this repository/project and why does it exist? | README identity sections: title/summary/`Why this exists` |
| How does a person install, configure, start or use it? | README usage sections: setup/run/use/configuration/public examples |
| What exists and who owns it? | `architecture.md` |
| What is integrated, blocked or next? | `current-state.md` |
| How does a durable feature behave? | `features/` when code/tests alone are not sufficient documentation |
| Why was a durable architectural choice made? | `adr/` |
| What substantial implementation is active now? | `workstreams/` |
| How should an agent perform a recurring procedure? | `skills/`, not docs |
| How does this repository setup/run/check/test/build/smoke/package/stop/clean internally? | `.engineering/commands.json`, not duplicated command policy in docs |
| What are the UX/brand/design-system constraints when `product-ui` is adopted? | `design/ux-contract.json` + `design/brand-kit.json` and the declared canonical design owner |
| What changed between two concrete build artifacts? | generated artifact `BUILD_CHANGELOG.md`, not project-status docs |
| What happened historically during implementation? | Git history |

## Documentation impact contract

Code and durable documentation ship together. A meaningful change is not complete until its documentation impact has been assessed and every affected canonical owner describes the system **as it exists after the change**.

Do not update every document mechanically. Update only owners whose durable truth changed, and record unaffected owners as `N/A` during preflight when the change could plausibly affect them.

Use this routing:

| Change affects | Update |
| --- | --- |
| core project purpose, primary audience or primary outcome | README identity sections |
| installation, prerequisites, first-run path, public configuration, public CLI/API/UI usage or copy-paste examples | README usage sections |
| durable non-obvious feature behavior, constraints or verification | existing/new `features/` owner |
| architecture boundaries or ownership | `architecture.md` |
| material durable decision/rationale | ADR |
| security/trust/data-lifecycle contract | `SECURITY.md` and/or owning architecture/feature doc |
| canonical operational command semantics | `.engineering/commands.json`; README usage only when a human-facing setup/run/use instruction also changed |
| product-experience/brand contract | owning `design/*` source when `product-ui` is adopted |
| repository current state | `current-state.md` |

### README section ownership

Treat the README as multiple semantic owners rather than one document that must be rewritten as a unit.

**Identity sections** describe what the project is, why it exists, its primary audience/outcome and stable positioning. Do not rewrite these merely because implementation details, commands, configuration or a feature workflow changed. Update identity only when the project itself changed in a way that makes those claims incomplete or misleading.

**Usage sections** describe what a user/developer must do now: prerequisites, setup, run/start, configuration, public API/CLI/UI flow, examples and the shortest successful path. These sections must change in the same change whenever the old instructions would fail, omit a newly required step, advertise a removed path, use stale defaults/options, or otherwise mislead someone using the current repository.

A README can therefore legitimately have `README_IDENTITY: N/A` and `README_USAGE: UPDATED` for the same feature change.

README summaries may link to canonical technical owners instead of duplicating detailed contracts. The README should remain a reliable human entry point, not an implementation diary or exhaustive architecture manual.

## Lifecycle

A fact has one canonical owner. Summaries link to that owner instead of duplicating detailed acceptance criteria or status.

Active workstream documents are disposable:

`plan -> implement -> validate -> transfer durable knowledge -> delete plan`

Keep a completed plan only when it has independent audit, regulatory, release or historical value; move such exceptional material to an explicitly historical location and never treat it as current truth.

Generated build manifests/deltas and E2E/visual regression screenshots/traces are evidence and follow artifact retention, not active documentation retention.

For `product-ui`, keep only bounded key reference views needed to communicate the product system. Do not use exported screenshot history as a parallel implementation/design source of truth.

## Before creating or updating documentation

1. Assess documentation impact from the observable change, not from filenames alone.
2. Search for an existing canonical owner.
3. Update it when the new fact fits its scope.
4. For README changes, update only the affected identity or usage sections; do not opportunistically rewrite stable identity copy.
5. Create a document only for a durable independently readable concern or an active bounded workstream.
6. Give active work a precise owner and `Read when` trigger.
7. Link it from this map or the closest domain index.
8. Delete obsolete temporary planning/mockup material and stale instructions/examples.

Do not create a document solely to say that a PR/task completed.
