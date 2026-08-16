# Contributing

## Change scope

Prefer the smallest coherent change that preserves repository invariants. Inspect the owning boundary, direct consumers and tests before changing shared contracts.

Use a workstream plan only when dependency/state coordination adds real value. Small changes should not create planning documents.

## Validation

Run the narrowest useful checks while iterating, then the required integration/repository gates for the changed blast radius. Do not suppress failing tests or weaken a gate merely to make a change green.

Run the repository health checks before publishing documentation/agent-governance changes:

```bash
python3 scripts/verify_repository.py
python3 scripts/verify_docs.py
python3 scripts/verify_agent_context.py
```

Add the project-specific format/lint/static/test/build commands here during adoption.

## Dependencies and architecture

Avoid dynamic versions and speculative dependencies. New abstractions/dependencies must have a concrete owner/problem and should not duplicate an existing source of truth.

## Pull requests

Keep PRs focused. Describe what changed, why, user/developer impact, relevant failure/resource implications, and validation executed. Do not claim hardware/device evidence that was not run.

Canonical branches should be protected with pull requests and required checks according to the project's branching/release model.
