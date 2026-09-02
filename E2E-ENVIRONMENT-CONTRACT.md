# E2E Environment Fidelity Contract

Version: 0.2.0

This contract defines how adopted repositories choose end-to-end execution environments and evidence so automated E2E remains representative enough for the claim without turning every change into release-grade media capture.

The governing rules are:

> Final target-environment validation should confirm residual environment-specific claims, not become the first time the complete workflow is exercised.

> E2E evidence strength follows the claim. Assertions, screenshots and full journey media are distinct evidence modes and must not be required mechanically when a cheaper mode proves the changed invariant.

This contract complements, rather than replaces, the project operating contract and execution-capability model.

- `OPERATING-CONTRACT.md` defines what `e2e` means and how the run behaves.
- `EXECUTION-CAPABILITY-CONTRACT.md` defines delivery stage, validation depth and who/where can execute a gate.
- `.engineering/e2e.json` defines target environments, execution environments, critical journeys, fidelity gaps and UI evidence policy.

## 1. Execution capability is not environment fidelity

These are independent dimensions.

Execution capability answers:

> Which executor can run this gate in the current session?

Environment fidelity answers:

> How closely does the environment used by the gate represent the target environment relevant to the product claim?

Examples:

| Evidence | Execution capability | Environment fidelity |
| --- | --- | --- |
| Android instrumentation on a CI emulator | `REMOTE_AUTOMATED` | `simulated_or_emulated` |
| Browser E2E on a supported virtual browser/OS | `REMOTE_AUTOMATED` | often `representative_virtual` |
| Automated physical device farm | `REMOTE_AUTOMATED` | `representative_physical` |
| Validation on the actual supported customer/device configuration | `REAL_ENVIRONMENT` | `target_environment` |

Never promote simulator/emulator evidence into a physical-device claim merely because the run is automated and green.

## 2. Fidelity classes

Use these stack-neutral fidelity classes in `.engineering/e2e.json`:

1. `host_or_fake` — useful whole-flow orchestration with material substitutes/fakes or a host environment that does not represent the target runtime.
2. `simulated_or_emulated` — a simulator/emulator reproduces important platform behavior but not all target hardware/runtime characteristics.
3. `representative_virtual` — the real software/runtime surface executes in a virtual/container/host environment representative enough for the stated claim.
4. `representative_physical` — the workflow executes on real hardware representative of the supported target class.
5. `target_environment` — the actual target environment or a configuration equivalent for the claim being validated.

Higher fidelity is not automatically better for every test. Prefer the cheapest reliable environment that proves the claim, then escalate only when a material target dimension requires it.

## 3. Environment dimensions

Representativeness is claim-specific. Declare only material dimensions, for example:

- operating system/platform and version family;
- CPU architecture/ABI;
- browser/engine;
- device class/form factor;
- native/runtime backend;
- GPU/NPU/accelerator availability;
- memory/storage constraints;
- permissions/sandbox/process lifecycle;
- network topology/external dependency;
- packaged/distributed artifact surface;
- hardware sensors/audio/camera peripherals;
- thermal/power conditions when they materially affect correctness or performance.

Two environments can share a fidelity class while representing different dimensions. Record known gaps explicitly.

## 4. Critical-journey design

E2E remains intentionally small. Keep deterministic local invariants in unit/integration/contract tests and select a bounded set of complete critical journeys whose correctness depends on the assembled system.

For every critical journey declare:

- the user/system outcome being claimed;
- whether it traverses a UI through `ui_surface`;
- the minimum UI evidence mode when a UI is involved;
- target environment(s);
- automated environment(s);
- minimum automated fidelity;
- residual fidelity gaps;
- whether real-environment confirmation is `required`, `conditional` or `not_required`.

When no automated environment can truthfully exercise a required journey, record the automation gap explicitly. Do not silently convert the workflow into an informal human test.

## 5. UI evidence modes

UI presence does not automatically imply that the UI itself is the changed claim.

Use one of three evidence modes:

### `ASSERTIONS`

Use when the UI is incidental to the workflow and the changed invariant is better proven by deterministic assertions.

Examples:

- Binder reconnect semantics exercised through an activity only as a harness;
- persistence/restart behavior where visual appearance is unchanged;
- background ownership or process-lifecycle behavior whose truth is not encoded in layout/motion.

Assertions may still emit failure screenshots opportunistically, but screenshots/video are not required merely because a UI process existed.

### `SCREENSHOTS`

Use when stable visible states, hierarchy, copy, layout, recovery states, adaptive behavior or another visually inspectable UI claim changed.

Capture only materially important checkpoints and the final reachable state. Screenshots remain bounded evidence, not a parallel design source of truth.

### `FULL_MEDIA`

Use when the claim depends on sequence over time or release-grade experience acceptance, including materially relevant:

- motion/animation;
- timing/progression/loading behavior;
- navigation or transition sequencing;
- foreground/background/lifecycle visibility;
- gesture continuity;
- release/product acceptance where complete journey replay is justified.

`FULL_MEDIA` includes the necessary stable screenshots plus one continuous journey video from meaningful start through success or terminal failure.

The selected mode may be stronger than the journey's minimum when the current change requires it.

## 6. Fidelity ladder

The preferred progression is:

```text
unit / component
    -> integration / contract
    -> automated E2E in the cheapest sufficient environment
    -> built/package-artifact E2E when material
    -> higher automated environment fidelity only when needed
    -> residual target/real-environment confirmation
```

Do not execute every rung mechanically. Validation follows delivery stage, blast radius and claim strength.

A final physical/manual test should primarily discover defects caused by the remaining fidelity delta: device-specific lifecycle, OEM/runtime behavior, real hardware, thermals, accelerator/backend differences, protected external environments, accessibility/usability judgement or similar constraints that cannot be reproduced earlier.

Ordinary workflow defects such as broken navigation, persistence, IPC/protocol integration, packaging/installability or basic restart/recovery should move into earlier automation whenever practical.

## 7. Built artifact and target surface

When the product claim concerns the distributable product, prefer E2E against the built/package artifact rather than only a development runner.

Artifact execution does not eliminate environment-fidelity gaps. A real APK on an emulator remains emulator evidence for hardware-dependent claims.

## 8. Selection and escalation

During `ITERATION`, `INTEGRATION` or `RELEASE`:

1. identify whether the change affects a critical journey;
2. read `.engineering/e2e.json`;
3. select the narrowest affected journey subset;
4. choose the UI evidence mode required by the changed claim;
5. use the cheapest declared automated environment that proves the claim;
6. escalate environment fidelity only when a material target dimension is missing;
7. escalate UI evidence to `FULL_MEDIA` only when sequence/time/release acceptance makes it necessary;
8. retain irreducible target-environment evidence as `REAL_ENVIRONMENT` and report it separately.

A repair that changes platform/runtime/packaging/native/hardware assumptions may legitimately escalate fidelity even if the original change did not.

## 9. Evidence

E2E evidence should identify enough context to understand what was actually proven:

- journey/test identity;
- source/build/run identity;
- execution environment ID;
- fidelity class;
- selected UI evidence mode;
- material platform/device/browser/runtime dimensions;
- artifact surface used;
- known gaps or residual real-environment requirement;
- relevant privacy-safe logs/traces/screenshots/videos.

Evidence completeness depends on the selected mode:

- `ASSERTIONS` — required deterministic assertions and ordinary failure evidence are present;
- `SCREENSHOTS` — assertions plus required screenshot checkpoints are present;
- `FULL_MEDIA` — assertions plus required screenshots and continuous journey video are present.

If a required artifact for the selected mode is missing, report `E2E_EVIDENCE_INCOMPLETE`. Do not silently downgrade the mode after execution to turn a run green.

On failure, preserve bounded useful evidence when technically possible. If the run fails before UI rendering or recording can begin, report the pre-UI/pre-recording failure truthfully rather than fabricating media.

All evidence must remain identity-bearing, privacy-safe and bounded by artifact-retention policy.

## 10. Platform specialization

Profiles may add the smallest platform-specific mapping without redefining the universal fidelity or evidence model.

Examples:

- Android distinguishes host tests, emulator/instrumentation, built APK on emulator, physical device farms and OEM/target evidence.
- macOS/iOS distinguishes host/unit execution, simulators where applicable, packaged app execution and real-device/platform evidence.
- browser/web distinguishes mocked/dev-server flows from supported browser/OS/deployment combinations.
- local-AI systems distinguish small deterministic model/runtime E2E from representative model/backend/hardware evidence for memory, throughput, thermals and accelerator-specific behavior.

The common requirement is **same semantics, native implementation**. Media capture should use established platform/framework tooling rather than forcing one universal library.

## 11. Completion rule

A critical journey is ready for the strongest product/release claim only when:

- required lower-level deterministic evidence passes;
- required automated E2E passes at the declared environment fidelity;
- evidence required by the selected UI evidence mode is complete;
- built/package execution is covered when material;
- residual fidelity gaps are explicit;
- required target/real-environment confirmation passes.

`AUTOMATED_PREFLIGHT_CONFIRMED` may still precede required real-environment evidence. It means automated evidence is complete for the selected integration/release claim, not that an unresolved physical/device/manual claim has passed.
