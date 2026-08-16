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
8. tests and architecture/contract invariants;
9. performance budgets/evidence;
10. reproducibility/build/package behavior;
11. repository hygiene;
12. CI/branch/release enforcement;
13. documentation lifecycle/canonical ownership;
14. agent operability and context/token cost.

## Evidence rules

- Inspect code, tests, CI/config and current canonical docs; do not grade from README claims alone.
- Distinguish documented policy from machine-enforced reality.
- Distinguish host/emulator/synthetic evidence from representative device/hardware evidence.
- Mark unavailable evidence as unknown/pending, not passing.
- Do not recommend a new abstraction/tool merely to satisfy the shape of the standard; recommend the simplest fix that closes a real invariant gap.

## Output

Produce:

- current maturity: L0/L1/L2 or `below L0`;
- strongest existing practices worth preserving;
- blocking gaps for the next level;
- important non-blocking risks/debt;
- agent-context observations (root/scoped guide size, active workstreams, duplicate/stale docs);
- a prioritized remediation DAG with dependencies and parallelizable lanes when meaningful;
- evidence required before claiming the next maturity level.

Prefer a short list of high-leverage gaps over a large generic checklist.
