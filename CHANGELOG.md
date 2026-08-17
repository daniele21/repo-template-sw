# Changelog

## 0.3.0 — 2026-08-17

Adds end-to-end validation as a first-class but stack-neutral part of the project operating contract:

- new canonical `e2e` command intent in `.engineering/commands.json`;
- E2E is recommended rather than universally mandatory, and may be `n/a` only when no meaningful whole-system/user journey exists;
- L1 expects automated end-to-end evidence for critical workflows when lower-level tests cannot establish the full outcome;
- L2 expects stronger coverage of critical journeys, representative failure/recovery paths and real artifact/device execution where applicable;
- E2E is explicitly distinct from `smoke`: smoke proves minimal runtime/artifact viability, E2E proves a complete workflow outcome;
- E2E runs inherit the zero-residue contract for processes, listeners, browser/device sessions, downloads, test data, temporary workspaces, logs, screenshots, traces and videos;
- failure evidence such as traces/screenshots/logs is treated as bounded CI artifact evidence with build/run identity;
- TypeScript/web guidance prefers Playwright for browser E2E unless an equally strong established solution already exists;
- Android guidance maps E2E to Compose UI Test/Espresso/UI Automator or the established native equivalent;
- macOS guidance maps E2E to XCTest/XCUITest or the established native equivalent;
- Python/server guidance maps E2E to real-process/API workflows rather than introducing browser tooling where no browser exists;
- validation Skills and agent routing now use the canonical `e2e` intent when the blast radius crosses a complete workflow boundary.

This remains **same semantics, native implementation**: the baseline requires the evidence boundary, not one universal E2E framework.

## 0.2.0 — 2026-08-16

Adds a common, stack-neutral project operating contract while preserving native tooling per repository:

- canonical command intents for `setup`, `doctor`, `dev`, `check`, `test`, `build`, `smoke`, `package`, `stop` and `clean`;
- machine-readable `.engineering/commands.json` contract;
- zero-dependency operating-contract validation in project and template CI;
- unique build identity and artifact-lineage semantics;
- immutable successful artifacts with staging/promote behavior, manifests and SHA-256 checksums;
- default local retention of the latest two successful builds per lineage;
- temporary CI-artifact vs durable release-artifact storage policy;
- generated `BUILD_CHANGELOG.md` delta for every successful comparable build;
- localhost/runtime ownership rules: loopback default, collision-aware ports, graceful shutdown and no residual project-owned listeners;
- zero-residue lifecycle rules for processes, sockets, locks, temp data, test databases, logs, caches and other ephemeral resources;
- repeatability and post-clean verification as reference-grade expectations;
- Android, macOS, Python, TypeScript and local-AI profile guidance aligned to the common command/lifecycle semantics.

This is a semantic baseline migration: adopted repositories must classify and implement the relevant operating-contract deltas rather than only bumping metadata.

## 0.1.0 — 2026-08-16

Initial agent-native reference engineering baseline:

- universal L0/L1/L2 engineering standard;
- project-local agent operating model;
- disposable workstream planning lifecycle;
- core reusable coding-agent skills;
- token and documentation budgets;
- zero-dependency repository health checks;
- adoption and update workflows;
- optional stack/domain profile model.
