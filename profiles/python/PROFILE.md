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

## Operating-contract mapping

Map existing Python tooling to `.engineering/commands.json`; do not add Make/Docker wrappers solely to normalize naming.

Typical mapping:

- `setup` — create/sync the project-local environment using the declared package manager/lockfile;
- `doctor` — verify supported Python/toolchain and required local/native dependencies;
- `dev` — start the supported application/server/runtime in foreground mode when applicable;
- `check` — formatter/linter/type/static gates;
- `test` — pytest/unittest/integration gates as owned by the project;
- `build` — build wheel/sdist/application/server bundle or other material artifact with unique build identity;
- `smoke` — execute/import/start the built artifact and exercise a minimal real path;
- `package` — create the intended distributable when distinct from `build`;
- `stop` — stop project-owned local server/processes when applicable;
- `clean` — remove project-owned virtual/build/staging/temp output without deleting unrelated user/global caches.

For localhost Python services, `dev` should bind to loopback by default, use a configurable collision-checked port, expose readiness/health when requests are accepted, and own all subprocesses/listeners. `stop`/failure/timeout/interrupt must verify that no project-owned listener remains.

Build output is staged then promoted on success, gets a unique build ID plus manifest/checksum/build delta, and defaults to the latest two successful local artifacts per comparable lineage.
