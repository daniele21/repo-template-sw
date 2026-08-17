# Product Experience Contract

Version: 0.4.0

This contract defines stack-neutral UX/UI expectations for repositories that expose a material user interface. It standardizes **experience quality and evidence**, not a visual fashion, component framework or design tool.

Adopt it through the optional `product-ui` profile.

The governing rule is:

> A strong interface makes the user's next decision obvious, reveals complexity progressively, communicates system state clearly, and remains consistent, accessible and recoverable across the product lifecycle.

## 1. Applicability

Use this contract for applications with a meaningful UI: web apps, mobile apps, desktop apps, embedded product surfaces and comparable interactive products. Do not force it onto headless libraries, APIs or servers with no material user-facing interface.

## 2. User task model over internal architecture

The UI should model what the user is trying to accomplish, not expose internal architecture by default. Prefer user concepts such as `Record meeting`, `Transcribe`, `Review`, `Export` over implementation concepts such as selecting backends, workers, sessions or queues.

Technical controls may exist when users genuinely need them, but should not dominate the default experience.

## 3. Contemporary and platform-appropriate, not trend-driven

The interface should feel current through clear hierarchy, restrained density, coherent typography, consistent spacing, intentional color, responsive/adaptive layout and platform conventions.

Do not encode short-lived visual trends as universal requirements. Glass effects, gradients, rounded cards, animation styles or other fashion-specific treatments are project choices, not baseline requirements.

## 4. Information architecture

Every important screen/surface should make these questions easy to answer:

- Where am I?
- What is happening?
- What can I do here?
- What is the primary action?
- What happens next?
- How do I go back, cancel or recover?

Navigation, page structure and labels should be organized around user goals and stable mental models.

## 5. Progressive disclosure

Reveal the minimum information and controls required for the user's current decision, while making deeper capability discoverable when needed.

Preferred hierarchy:

```text
essential
  -> contextual
  -> advanced
  -> expert / diagnostics
```

Avoid placing essential, advanced and debugging controls at the same visual level. Advanced configuration, raw data, logs, technical metrics and diagnostics should not dominate normal user journeys unless the product is explicitly an expert tool whose users need them continuously.

## 6. Cognitive load and information density

Completeness is not permission to show everything simultaneously.

Prefer summary -> detail, overview -> drill-down, primary -> secondary -> tertiary action hierarchy, normal use -> advanced configuration, and status -> diagnostics.

Avoid unnecessary walls of text, repeated labels, competing cards, excessive badges, simultaneous metrics and multiple equally dominant calls to action. Dense expert views are allowed when the workflow genuinely benefits from density, but the density must be intentional and navigable.

## 7. Sensible defaults

Normal use should work with strong defaults before the user configures every option.

```text
good default -> normal use
optional customization -> advanced user
expert override -> deep settings
```

Do not make users understand internal implementation details merely to complete the primary workflow.

## 8. Action hierarchy and affordances

Each surface should have an intentional hierarchy of primary, secondary, tertiary and destructive actions. Controls should look and behave like their semantic role. Repeated interaction patterns should remain consistent across the product.

## 9. Complete state design

Critical components and workflows must consider more than the happy path. Applicable states include:

```text
default
hover
focus
pressed
selected
disabled
loading
empty
success
warning
error
offline
permission-denied
partial-result
```

A state may be `n/a` when the platform/component cannot enter it, but unhandled loading/error/empty/disabled states are not acceptable for critical workflows.

## 10. Feedback and perceived performance

Every meaningful user action should produce timely, understandable feedback.

```text
action
  -> immediate acknowledgement
  -> progress/status when needed
  -> success/failure outcome
  -> clear next action
```

Use progress information when it is truthful and useful. Long-running work should not be represented by an indefinite spinner when meaningful phase/progress/status information is available. Background operations should expose persistent status when the user needs to know they are still running or completed.

## 11. Error prevention and recovery

Prefer prevention over warning, warning over failure, and recovery over dead ends.

Error messages should explain, when known, what failed, why or what constraint was violated, and what the user can do next. Use undo, reversible actions or recoverable states when safer and simpler than confirmation dialogs. Confirmation is appropriate when an action is irreversible or has meaningful external impact.

## 12. Accessibility

Accessibility is part of correctness, not visual polish.

Web products should target WCAG 2.2 AA or a stronger explicitly documented target. Native products should use platform accessibility APIs/guidelines and equivalent semantic requirements.

Applicable requirements include sufficient contrast, keyboard operability where relevant, visible/logical focus order, semantic assistive labels, text scaling/dynamic type, appropriately sized interaction targets, no critical meaning conveyed by color alone, reduced-motion support when animation is non-essential, and accessible error/status announcements.

Automated accessibility checks are useful but do not replace manual/assistive-technology validation for important flows.

## 13. Responsive and adaptive behavior

The interface should preserve content priority as available space, device posture, window size or input method changes. Do not treat responsive design as shrinking a desktop layout until it fits.

Projects should define supported layout classes/breakpoints/window ranges or platform-native adaptive behavior and test the important ones.

## 14. Brand kit

A UI product should define a durable brand/visual identity source of truth when branding is applicable.

The brand kit should cover as applicable product name, logo variants, application icon, favicon for browser products, semantic color system, typography, spacing scale, radius/elevation treatment, iconography style, motion principles, light/dark theme behavior, imagery/illustration style, and voice/microcopy principles.

Use semantic design tokens rather than scattering raw visual values through the codebase. Prefer names such as `color.surface`, `color.textPrimary`, `color.primary`, `color.success`, `color.warning`, `color.error`, `color.border` and `color.focus`.

## 15. Design system

Brand identity describes how the product looks; the design system describes how the UI is constructed.

Projects should identify canonical reusable components and their states/variants rather than creating visually similar one-off controls repeatedly.

Typical primitives include buttons, inputs, selects, toggles, navigation, tabs, cards, lists/tables, dialogs, popovers, toasts, progress, tooltips, empty states and error states.

> Do not create a new visual component when the existing design system can express the same semantic role without harming usability.

## 16. Design source of truth and key reference views

The repository must declare the canonical source for product design: Figma, design files in-repo, code-first design system or another explicit owner.

Do not accumulate uncontrolled screenshot versions such as `final2`, `new-final` or duplicate mockup folders.

Maintain only the key reference views necessary to communicate the product system, for example the primary/home surface, main workspace, critical settings/configuration surface, representative loading/empty/error states, and critical user journeys.

Mockups are reference evidence, not parallel truth that silently diverges from production UI.

## 17. Critical user journeys

UI projects should explicitly identify their critical journeys: complete flows whose failure would materially damage the product experience. Examples may include first run/onboarding, primary create/use/save flow, persistence/restart, import/export, destructive/recovery behavior, authentication where applicable, or the product's core task.

Critical journeys should connect to the repository's `e2e` evidence strategy when lower-level tests cannot establish the full user outcome.

## 18. UX validation and regression protection

Use the narrowest useful evidence for the claim being made. Depending on the stack/project, useful evidence includes component/unit tests, integration tests, E2E for critical journeys, automated accessibility checks, visual-regression tests for stable high-value surfaces, manual keyboard/screen-reader/device checks, and representative-user usability tests for important/high-risk workflows.

Visual-regression testing should protect important visual contracts, not freeze every incidental pixel and make intentional design change unnecessarily painful.

## 19. E2E and experience evidence lifecycle

UI E2E/visual/accessibility evidence follows the same identity and zero-residue principles as the Project Operating Contract.

A run should have enough identity to associate evidence with source/build/environment. Failed E2E may retain screenshots, traces, videos or logs as bounded CI artifacts.

After the run, project-owned browser/device sessions, servers, ports, downloads, test users/data, temporary profiles and other ephemeral state must be cleaned.

## 20. Microcopy and language

UI text is part of the interaction contract. Prefer concise, concrete labels based on user goals. Avoid exposing internal implementation terminology without user value. Error/status text should help the user decide what to do next. Terminology should remain consistent across navigation, controls, documentation and errors.

## 21. Debug and expert surfaces

Diagnostics are valuable, especially in technical/local-AI products, but they should be intentionally separated from normal product interaction. Expose raw logs, JSON, low-level metrics and expert configuration through advanced/diagnostic surfaces unless they are central to the user's primary job.

## 22. Experience maturity

For repositories adopting `product-ui`:

### L0 — Healthy product UI

- product experience contract is specialized for the project;
- brand/design source of truth is identified;
- core information architecture and critical journeys are named;
- primary action hierarchy and progressive disclosure are intentional;
- critical loading/empty/error/disabled states exist;
- accessibility target is declared;
- responsive/adaptive scope is declared;
- key design/reference views are identified;
- design tokens/component ownership prevent obvious UI drift.

### L1 — Production-ready product UI

L0 plus:

- critical journeys have automated E2E evidence when lower-level tests are insufficient;
- important accessibility behavior has automated and/or manual evidence appropriate to the platform;
- high-value responsive/adaptive layouts are tested;
- user-facing failures provide actionable recovery paths;
- visual regression protects stable high-risk surfaces where valuable;
- primary flows have been heuristically reviewed against this contract.

### L2 — Reference-grade product UI

L1 plus:

- representative-user usability evidence exists for important or high-risk workflows where justified;
- critical UX regressions are protected by E2E/accessibility/visual evidence appropriate to the product;
- product telemetry/research can answer meaningful experience questions without compromising privacy;
- design-system drift and duplicated components/tokens are actively controlled;
- significant UX changes evaluate cognitive load, information hierarchy and recovery behavior rather than only visual appearance.

## 23. Product experience Definition of Done

A UI-affecting change is complete only when the applicable experience claim is supported across:

```text
TASK MODEL
-> INFORMATION HIERARCHY
-> STATES / FEEDBACK
-> ACCESSIBILITY
-> ADAPTIVE LAYOUT
-> DESIGN SYSTEM / BRAND
-> E2E / REGRESSION EVIDENCE
-> CLEANUP / ARTIFACT EVIDENCE
```

Not every UI change needs every level, but no applicable level should be silently skipped.
