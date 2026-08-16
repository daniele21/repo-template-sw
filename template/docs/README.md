# Documentation map

Use progressive disclosure. Start from `AGENTS.md`, add the closest scoped agent guide, then read only the canonical source required by the task.

## Canonical owners

| Question | Source |
| --- | --- |
| What exists and who owns it? | `architecture.md` |
| What is integrated, blocked or next? | `current-state.md` |
| How does a durable feature behave? | `features/` when code/tests alone are not sufficient documentation |
| Why was a durable architectural choice made? | `adr/` |
| What substantial implementation is active now? | `workstreams/` |
| How should an agent perform a recurring procedure? | `skills/`, not docs |
| What happened historically during implementation? | Git history |

## Lifecycle

A fact has one canonical owner. Summaries link to that owner instead of duplicating detailed acceptance criteria or status.

Active workstream documents are disposable:

`plan -> implement -> validate -> transfer durable knowledge -> delete plan`

Keep a completed plan only when it has independent audit, regulatory, release or historical value; move such exceptional material to an explicitly historical location and never treat it as current truth.

## Before creating a document

1. Search for an existing canonical owner.
2. Update it when the new fact fits its scope.
3. Create a document only for a durable independently readable concern or an active bounded workstream.
4. Give active work a precise owner and `Read when` trigger.
5. Link it from this map or the closest domain index.
6. Delete obsolete temporary planning material.

Do not create a document solely to say that a PR/task completed.
