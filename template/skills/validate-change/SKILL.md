---
name: validate-change
description: Select and execute the narrowest sufficient validation for a change while iterating, then expand to the correct final integration, repository, device or hardware gate based on blast radius and claims.
---

# Validate Change

## Principle

Do not run the entire repository for every edit, and do not stop at a local unit test when a shared contract or runtime boundary changed. Validation follows blast radius and the strength of the claim.

## Validation ladder

### Level A — local iteration

Use for private implementation inside one owner:

- formatter/linter for touched surface;
- focused unit tests;
- module/package compile or typecheck.

### Level B — direct consumers

Add when a contract or behavior affects known callers/adapters:

- direct consumer tests;
- contract/fake compatibility;
- persistence/migration tests if applicable;
- affected UI/transport compilation.

### Level C — integration/repository

Add for public contracts, multiple domains, build/configuration, CI/tooling or broad dependency changes:

- repository formatting/static/type gates;
- full relevant test suite;
- integration/contract tests;
- production build/package validation.

### Level D — real environment evidence

Required for claims that CI/host tests cannot truthfully prove:

- physical device/hardware behavior;
- memory reclamation/unified/GPU footprint;
- audio/device routing;
- performance/thermal characteristics;
- platform packaging/signing/runtime behavior;
- external-service integration where a real environment is part of the claim.

Synthetic/emulator evidence must be labelled as such and cannot satisfy a stronger claim.

## Workflow

1. Identify changed owner and public blast radius.
2. Read the nearest agent guide for project-specific commands.
3. Run the cheapest deterministic gate that can falsify the current edit quickly.
4. Expand only when the change crosses a boundary or is ready for final integration.
5. If a gate cannot run, record the exact missing dependency/environment and do not silently treat it as passed.
6. Never weaken/delete/suppress a legitimate failing test to make the change green without addressing the owning behavior or explicitly changing the contract.
7. Report exact validation executed and evidence still pending.

## Output

A final change summary should distinguish:

- PASS — executed and passed;
- FAIL — executed and failed;
- PENDING — required but unavailable/not executed;
- N/A — genuinely not applicable.

This distinction prevents an agent from converting absence of evidence into evidence of correctness.
