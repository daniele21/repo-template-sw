# Product design contract

This directory is used when the repository adopts the optional `product-ui` profile.

Canonical files:

- `ux-contract.json` — machine-readable UX/product-experience expectations specialized for the project;
- `brand-kit.json` — semantic visual identity/design-token source used by the project or linked to the true design-system owner.

Keep only durable design truth and key reference views here. Do not accumulate exported screenshots, duplicate mockup revisions or generated design artifacts as a parallel source of truth.

The project must identify the canonical design owner (for example Figma, code-first design system or in-repo source files) in `ux-contract.json`.

When useful, add a small `reference/` directory containing only key views such as the primary surface, main workspace and representative loading/empty/error states. Generated regression screenshots belong in CI artifacts/evidence with bounded retention, not normal source history.
