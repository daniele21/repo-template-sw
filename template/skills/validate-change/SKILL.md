---
name: validate-change
description: Select and execute the narrowest sufficient validation for a change while iterating, then expand to the correct final integration, end-to-end, repository, artifact, device or hardware gate based on blast radius and claims.
---

# Validate Change

## Principle

Do not run the entire repository for every edit, and do not stop at a local unit test when a shared contract or runtime boundary changed. Validation follows blast radius and the strength of the claim.

Use `.engineering/commands.json` as the canonical repository-level command routing surface. Do not invent alternate build/test/E2E/start commands when the project already declares them there.

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

- canonical `check` command;
- canonical `test` command or the relevant scoped subset;
- integration/contract tests;
- canonical `build` when build/runtime/package behavior may be affected;
- operating-contract/repository-health validation.

### Level D — end-to-end/product flow

Add when the claim crosses a complete user/system workflow boundary and lower-level tests cannot establish the final outcome:

- canonical `e2e` command or the smallest relevant critical journey subset;
- complete workflow assertion through the real public/UI/protocol boundary;
- built/package artifact execution when the claim depends on distributable behavior and this is technically practical;
- zero-residue cleanup of app/server/browser/device/test state owned by the run;
- bounded failure evidence with build/run/environment identity.

Do not require E2E for every change. Prefer unit/integration coverage when it can prove the same invariant more deterministically and cheaply.

### Level E — real environment evidence

Required for claims that CI/host tests cannot truthfully prove:

- physical device/hardware behavior;
- memory reclamation/unified/GPU footprint;
- audio/device routing;
- performance/thermal characteristics;
- platform packaging/signing/runtime behavior;
- external-service integration where a real environment is part of the claim.

Synthetic/emulator evidence must be labelled as such and cannot satisfy a stronger claim.

## Smoke vs E2E

A build passing is not equivalent to the built artifact working, and smoke is not equivalent to E2E.

- `smoke` proves minimal viability: start/install/launch -> minimal request/path -> stop;
- `e2e` proves a complete critical workflow outcome across the assembled system.

Use both when both claims matter.

## Operational validation

When the change affects runtime/build/package/E2E/lifecycle behavior, validate the applicable operating-contract invariants:

- a material build has a unique build identity;
- artifact name/manifest identify product version, build ID and source revision;
- successful artifact is promoted only after validation and is not modified in place;
- `BUILD_CHANGELOG.md` compares against the previous successful comparable build;
- local artifact retention is applied after successful promotion;
- `dev`/`e2e`/`smoke`/`stop` leave no project-owned child process or listener behind;
- browser/device profiles, test data, downloads, temporary workspaces, locks and other owned ephemeral resources are cleaned after success and failure paths;
- E2E traces/screenshots/videos/logs have bounded retention and do not become permanent repository clutter;
- failed/partial artifacts cannot be mistaken for valid outputs.

For localhost services, a strong smoke test is: start -> readiness -> minimal request -> graceful stop -> verify process/children/listener gone -> verify temporary resources clean.

A strong E2E extends that lifecycle with one complete critical workflow before the same cleanup verification.

## Workflow

1. Identify changed owner and public blast radius.
2. Read the nearest agent guide and `.engineering/commands.json` for canonical commands.
3. Run the cheapest deterministic gate that can falsify the current edit quickly.
4. Expand only when the change crosses a boundary or is ready for final integration.
5. Use E2E only when the full product/system outcome is part of the claim.
6. If a gate cannot run, record the exact missing dependency/environment and do not silently treat it as passed.
7. Never weaken/delete/suppress a legitimate failing test to make the change green without addressing the owning behavior or explicitly changing the contract.
8. Report exact validation executed and evidence still pending.

## Output

A final change summary should distinguish:

- PASS — executed and passed;
- FAIL — executed and failed;
- PENDING — required but unavailable/not executed;
- N/A — genuinely not applicable.

This distinction prevents an agent from converting absence of evidence into evidence of correctness.
