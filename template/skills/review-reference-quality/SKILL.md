---
name: review-reference-quality
description: Audit a repository or milestone against the Agent-Native Reference Engineering Standard and produce a prioritized evidence-based L0/L1/L2 gap assessment without adding speculative machinery.
---

# Review Reference Quality

Use before important releases, after major architecture change, during standard adoption, or when the user asks whether the repository is solid/reference-grade.

## Review dimensions

Assess evidence for:

1. architecture and ownership;
2. complexity/dependency discipline;
3. resource/memory lifecycle;
4. concurrency/backpressure;
5. failure/cancellation/shutdown/recovery;
6. data lifecycle, privacy and security;
7. observability and error taxonomy;
8. layered testing and architecture/contract invariants;
9. E2E coverage of critical workflows when the complete outcome cannot be proven below that level;
10. performance budgets/evidence;
11. reproducibility/build/package behavior;
12. project operating contract and canonical command routing;
13. build identity, artifact lineage, retention, manifests/checksums and build deltas;
14. local runtime/port/process ownership and zero-residue cleanup;
15. repository hygiene;
16. CI/branch/release enforcement;
17. documentation lifecycle/canonical ownership;
18. agent operability and context/token cost.

## Evidence rules

- Inspect code, tests, CI/config and current canonical docs; do not grade from README claims alone.
- Read `.engineering/commands.json` and verify that declared commands map to real project behavior.
- Distinguish unit/integration/E2E/smoke evidence instead of treating all green tests as equivalent.
- E2E should be a small set of critical workflows, not a requirement to automate every UI path.
- If lower-level tests fully prove a workflow invariant, do not recommend E2E merely for compliance aesthetics.
- If a critical product outcome crosses multiple real boundaries and has no automated complete-workflow evidence, treat that as an L1 gap unless a concrete reason makes automation inappropriate.
- Browser/web projects should prefer Playwright when adding new browser E2E unless an equally strong established solution already exists; do not replace a good incumbent tool without cause.
- Distinguish documented policy from machine-enforced reality.
- For runtime/build/E2E lifecycle claims, inspect cleanup paths for success, failure, timeout, cancellation and interrupt.
- Verify that E2E failure traces/screenshots/videos/logs are privacy-safe, identity-bearing and bounded-retention artifacts rather than repository clutter.
- Verify that successful artifacts are identifiable/immutable and that local retention is bounded.
- Verify that build deltas compare against the previous successful comparable build rather than only showing a generic Git log.
- For localhost runtimes, distinguish a closed application listener from normal kernel states such as `TIME_WAIT`.
- Distinguish host/emulator/synthetic evidence from representative device/hardware evidence.
- Mark unavailable evidence as unknown/pending, not passing.
- Do not recommend a new abstraction/tool merely to satisfy the shape of the standard; recommend the simplest fix that closes a real invariant gap.

## Output

Produce:

- current maturity: L0/L1/L2 or `below L0`;
- strongest existing practices worth preserving;
- blocking gaps for the next level;
- important non-blocking risks/debt;
- test-layer observations (unit/integration/E2E/smoke, critical journeys and evidence strength);
- operating-contract observations (command coverage, build/artifact identity, runtime/cleanup, retention and build delta);
- agent-context observations (root/scoped guide size, active workstreams, duplicate/stale docs);
- a prioritized remediation DAG with dependencies and parallelizable lanes when meaningful;
- evidence required before claiming the next maturity level.

Prefer a short list of high-leverage gaps over a large generic checklist.
