# Feature documentation

Feature documents describe durable current behavior, constraints, ownership and verification when those facts are not sufficiently discoverable from public contracts, tests and architecture documentation.

Do not create one file per small feature. Prefer code/tests for obvious behavior and a bounded document for cross-module or operationally important capabilities.

A feature document should normally answer:

- user/system outcome;
- canonical owner and important consumers;
- public/domain contract;
- persistence/data lifecycle when relevant;
- resource/failure semantics when relevant;
- important constraints;
- verification/evidence.

It must not contain implementation progress, PR history or a completed task diary.
