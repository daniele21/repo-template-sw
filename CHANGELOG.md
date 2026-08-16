# Changelog

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
