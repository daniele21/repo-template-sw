# E2E Environment Fidelity Contract

Version: 0.1.1

This contract defines how adopted repositories choose end-to-end execution environments so automated E2E evidence becomes progressively representative of the real target rather than leaving ordinary whole-system defects for final manual/device validation.

The governing rule is:

> Final target-environment validation should confirm residual environment-specific claims, not become the first time the complete workflow is exercised.

For critical journeys that traverse a user interface, there is an additional evidence rule:

> A UI E2E journey is not complete evidence unless the run produces both inspectable UI screenshots and a complete journey video as bounded artifacts.

This contract complements, rather than replaces, the project operating contract and execution-capability model.

- `OPERATING-CONTRACT.md` defines what `e2e` means and how the run behaves.
- `EXECUTION-CAPABILITY-CONTRACT.md` defines who/where can execute a gate for the current agent/session: `AGENT_LOCAL`, `REMOTE_AUTOMATED` or `REAL_ENVIRONMENT`.
- `.engineering/e2e.json` defines the target environments, E2E execution environments, fidelity gaps and critical journeys for the adopted repository.

## 1. Execution capability is not environment fidelity

These are two independent dimensions.

Execution capability answers:

> Which executor can run this gate in the current session?

Environment fidelity answers:

> How closely does the environment used by the gate represent the target environment relevant to the product claim?

Examples:

| Evidence | Execution capability | Environment fidelity |
| --- | --- | --- |
| Android instrumentation on a CI emulator | `REMOTE_AUTOMATED` | `simulated_or_emulated` |
| Browser E2E on the supported browser/OS combination in CI | `REMOTE_AUTOMATED` | often `representative_virtual` |
| Automated run on a physical device farm | `REMOTE_AUTOMATED` | `representative_physical` |
| Manual validation on the actual supported customer/device configuration | `REAL_ENVIRONMENT` | `target_environment` |
| API workflow against a locally started real service | `AGENT_LOCAL` or `REMOTE_AUTOMATED` | depends on which target dimensions are represented |

Never promote emulator/simulator evidence into a physical-device claim merely because the run is automated and green.

## 2. Fidelity classes

Use these stack-neutral fidelity classes in `.engineering/e2e.json`:

1. `host_or_fake` — useful whole-flow orchestration with material substitutes/fakes or a host environment that does not represent the target runtime.
2. `simulated_or_emulated` — a simulator/emulator reproduces important platform behavior but not all target hardware/runtime characteristics.
3. `representative_virtual` — the real software/runtime surface executes in a virtual/container/host environment representative enough for the stated claim.
4. `representative_physical` — the workflow executes on real hardware representative of the supported target class, but not necessarily the exact final/customer environment.
5. `target_environment` — the actual target environment or a configuration equivalent for the claim being validated.

Higher fidelity is not automatically better for every test. Prefer the cheapest reliable environment that proves the claim during normal iteration, then use stronger environments for the critical journeys and residual risks that need them.

## 3. Environment dimensions

Representativeness is claim-specific. A project should declare only material dimensions, for example:

- operating system/platform and version family;
- CPU architecture/ABI;
- browser/engine;
- device class/form factor;
- native/runtime backend;
- GPU/NPU/accelerator availability;
- memory/storage constraints;
- permissions/sandbox/process lifecycle;
- network topology/external dependency;
- database/service implementation;
- packaged/distributed artifact surface;
- hardware sensors/audio/camera peripherals;
- thermal/power conditions when they materially affect correctness or performance.

Two environments can have the same fidelity class while differing in which dimensions they represent. Record known gaps rather than relying on the class label alone.

## 4. Critical-journey design

E2E remains intentionally small. Keep deterministic local invariants in unit/integration/contract tests and select a bounded set of complete critical journeys whose correctness depends on the assembled system.

For every critical journey declare:

- the user/system outcome being claimed;
- the target environment(s) relevant to that claim;
- the automated environment(s) used before final target validation;
- the minimum automated fidelity expected for normal release confidence;
- known fidelity gaps that automation does not cover;
- whether real-environment confirmation is `required`, `conditional` or `not_required`.

When the journey traverses a material UI, the E2E implementation must also define stable screenshot checkpoints and record the complete journey video. Keep screenshot checkpoints outcome-oriented rather than mirroring every implementation step. At minimum, retain enough screenshots to inspect the materially important UI states and the final user-visible outcome, while the video must cover the journey continuously from its meaningful start through success or the terminal failure state.

When no automated environment can truthfully exercise a required journey, record the automation-capability gap explicitly. Do not silently convert the entire workflow into an informal human test.

## 5. Fidelity ladder

The preferred progression is:

```text
unit / component
    -> integration / contract
    -> automated E2E in the cheapest sufficient environment
    -> built/package-artifact E2E when material
    -> highest practical automated environment fidelity
    -> residual target/real-environment confirmation
```

This is not a requirement to execute every rung on every change. Validation still follows blast radius and cost. The requirement is that the repository intentionally decides which rung proves each critical claim and what remains genuinely environment-specific.

A final physical/manual test should primarily discover defects caused by the remaining fidelity delta: device-specific lifecycle, OEM/runtime behavior, real hardware, thermals, accelerator/backend differences, protected external environments, accessibility/usability judgement or similar constraints that cannot be faithfully reproduced earlier.

Ordinary workflow defects — broken navigation, persistence, IPC/protocol integration, packaging/installability, request/response wiring or basic restart/recovery behavior — should be moved into earlier automated E2E whenever technically practical.

## 6. Built artifact and target surface

When the product claim concerns the distributable product, prefer E2E against the built/package artifact rather than only a development runner.

Examples:

- install the produced APK rather than only invoking source-level Android components;
- exercise the packaged desktop app when packaging/runtime entitlements are part of the claim;
- run the production-like server/container artifact where configuration/startup behavior matters;
- exercise the supported browser build/deployment shape rather than a mocked UI shell when the deployed integration is part of the claim.

Artifact execution does not eliminate environment-fidelity gaps. A real APK on an emulator still remains emulator evidence for hardware-dependent claims.

## 7. Selection and escalation

During iteration and preflight:

1. identify whether the change affects a critical journey;
2. read `.engineering/e2e.json`;
3. select the narrowest E2E journey subset that covers the blast radius;
4. use the cheapest declared automated environment that can prove the changed claim;
5. escalate fidelity when the changed invariant depends on a dimension missing from that environment;
6. retain any genuinely irreducible target-environment evidence as `REAL_ENVIRONMENT` and report it separately.

A repair that changes platform/runtime/packaging/native/hardware assumptions may require fidelity escalation even if the original change did not.

## 8. Evidence

E2E evidence should identify enough context to understand what was actually proven:

- journey/test identity;
- source/build/run identity;
- execution environment ID from `.engineering/e2e.json`;
- fidelity class;
- material platform/device/browser/runtime dimensions;
- artifact surface used;
- known gaps or residual real-environment requirement;
- relevant privacy-safe logs/traces/screenshots/videos.

For every critical journey that traverses a UI, **screenshots and video are both required evidence artifacts**, not optional decoration:

- capture stable, materially important screenshot checkpoints and always include the final reachable user-visible outcome for a successful run;
- record one continuous video covering the meaningful journey from start through the final success state or terminal failure state, so navigation, transitions, loading/progress behavior and interaction sequencing remain inspectable;
- preserve screenshot/video filenames or metadata so both can be tied to the journey, run/build identity and execution environment rather than becoming anonymous media;
- publish/retain them through the repository's normal local/CI artifact mechanism with bounded retention; they are evidence, not durable design documentation or a second design source of truth;
- keep both artifact types privacy-safe and free of secrets or unnecessary sensitive user data;
- on failure, preserve the last useful screenshot state when technically possible and the video up to the failure, together with normal failure trace/log evidence; if the run fails before any UI can render or before recording can start, report the pre-UI/pre-recording failure truthfully rather than fabricating evidence.

A UI journey whose assertions executed but whose required screenshots **or** video are missing has **incomplete E2E evidence**. It must not be reported as a complete E2E PASS for preflight/release readiness until both artifact classes are present, except for a truthfully classified pre-UI/pre-recording failure where those artifacts could not exist.

Do not report a generic `E2E PASS` when materially different environment claims remain unresolved.

## 9. Platform specialization

Profiles may add the smallest platform-specific mapping without redefining the universal fidelity model.

Examples:

- Android distinguishes host tests, emulator/instrumentation, physical device farms and representative physical/OEM evidence.
- macOS/iOS distinguishes host/unit execution, simulators where applicable, packaged app execution and real-device/platform evidence.
- browser/web distinguishes mocked/dev-server flows from supported browser/OS/deployment combinations.
- local-AI systems distinguish small deterministic model/runtime E2E from representative model/backend/hardware evidence for memory, throughput, thermals and accelerator-specific behavior.

The common requirement is **same semantics, native implementation**. UI screenshot and video capture should use the established platform/framework-native E2E tooling rather than forcing one universal media library.

## 10. Completion rule

A critical journey is ready for the strongest product/release claim only when:

- required lower-level deterministic evidence passes;
- required automated E2E passes at the declared environment fidelity;
- for UI journeys, required screenshot and video artifacts are present, identity-bearing and privacy-safe;
- built/package execution is covered when material;
- residual fidelity gaps are explicitly known;
- required target/real-environment confirmation passes.

`AUTOMATED_PREFLIGHT_CONFIRMED` may still precede required real-environment evidence. It means automated evidence is complete, not that an unresolved physical/device/manual claim has magically passed.
