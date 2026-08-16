# Python profile

Add only when Python is a material implementation/runtime surface.

Minimum additions to the universal baseline:

- one canonical package/build configuration, normally `pyproject.toml`;
- committed lockfile for applications/tools where reproducibility matters;
- explicit supported Python versions;
- formatter/linter/static or type checks appropriate to the codebase;
- deterministic unit/integration tests with no hidden network dependency unless explicitly marked;
- CI matrix only across versions the project actually claims to support;
- virtual environments/caches/build artifacts excluded from source control;
- subprocesses, threads, temp files and external clients follow the resource lifecycle contract;
- packaging entry points and runtime data files validated from a clean environment.

Prefer standard-library or already-adopted tooling when it is sufficient; do not add overlapping lint/test/package systems without a concrete need.
