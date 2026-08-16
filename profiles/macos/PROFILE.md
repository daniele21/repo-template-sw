# macOS profile

Add only when macOS APIs, packaging or runtime behavior are material.

Minimum additions:

- explicit supported macOS/architecture targets;
- composition/lifecycle ownership for native windows, menu-bar processes, background servers, audio/video devices and helper processes;
- centralized dev-vs-bundle path resolution;
- packaging includes all runtime data/binaries and is tested from the built artifact;
- signing/notarization secrets remain external to source control;
- interruption, crash and shutdown restore temporary system/device state where applicable;
- representative Apple hardware evidence for device/audio/GPU behavior that host-only tests cannot establish.
