# Local AI profile

Add when the project loads or orchestrates local ML/LLM/ASR/vision models.

Minimum additions:

- canonical artifact/model identity and compatibility validation before expensive load;
- explicit runtime states and one owner for model/session residency;
- memory/resource estimates are separate from observations; unavailable measurements are not zero;
- deterministic admission/reservation before expensive allocation where possible;
- bounded concurrency, queues and backpressure for inference/decoding jobs;
- active/pinned work is protected from incompatible unload/eviction;
- unload, cancellation, startup failure and shutdown release resources deterministically;
- no implicit cloud fallback; remote providers are explicit trust-boundary choices;
- prompts, generated content, audio/images and user payloads stay out of normal telemetry by default;
- metrics may include resident models, memory/VRAM/unified memory, active jobs, queue depth, load time, TTFT, throughput, cache hits and eviction reason;
- performance/resource claims require representative hardware evidence;
- benchmark/test results include durable model, dataset, configuration and code identity sufficient for reproduction.

## Operating-contract mapping

The common project operating contract also applies to model runtimes and local inference servers.

- `doctor` should report model/backend/hardware prerequisites truthfully without treating unavailable resource telemetry as zero;
- `dev`/`smoke`/`e2e` must own model-server processes, helper processes, sockets, ports, temporary model/session state and reservations;
- `e2e` should exercise a complete critical model workflow when correctness depends on multiple assembled stages, for example load -> infer/transcribe -> persist/return result -> release, rather than only checking one backend function;
- expensive model loads must not begin before required admission/compatibility checks;
- `stop`, cancellation, timeout, startup failure and interrupt must release listeners, reservations, sessions and resident resources owned by the run;
- post-stop verification must distinguish an actually closed project listener/process from normal kernel states;
- build/runtime artifacts, E2E evidence and benchmarks must preserve software build identity plus model/dataset/configuration identity.

Prefer deterministic fixtures/small representative models for routine E2E when they preserve the workflow invariant. Use representative production hardware/model evidence separately when the claim depends on memory, throughput, thermals or backend-specific behavior.

For model caches, distinguish durable user-selected model storage from ephemeral build/test/E2E/runtime cache. Do not delete durable model artifacts during generic `clean`; only clean resources whose project/run ownership is explicit.

When a local-AI project produces distributable application/server artifacts, use the standard artifact lifecycle: unique build identity, immutable successful artifact, manifest/SHA-256, build delta, bounded local retention and durable release storage.
