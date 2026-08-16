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
