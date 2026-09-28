# Release Notes

This document provides structured release notes for each official version tag created in the Checkout Service repository. It links version tags to concrete changes and establishes clear rollback targets for incident mitigation.

---

## Release v1.1.1

- **Release Date:** 2026-06-06
- **Git Tag:** `v1.1.1` (Annotated)
- **Target Commit:** `82535d9`
- **Rollback Target:** `v1.1.0`

### Summary of Changes
This patch release improves the runtime visibility of the Checkout Service by introducing explicit service identifiers and version constants.

#### Changed
- Refactored `src/app.js` to define `SERVICE_NAME` (`checkout-service`) and `SERVICE_VERSION` (`0.0.0-placeholder`).
- Updated startup logging to display service name and version explicitly on boot.

#### Fixed
- Standardized startup function declaration from `runCheckout()` to modular `run()`.

---

## Release v1.1.0

- **Release Date:** 2026-06-06
- **Git Tag:** `v1.1.0` (Annotated)
- **Target Commit:** `86989b6`
- **Rollback Target:** `v1.0.0`

### Summary of Changes
This minor release introduces the operational observability and documentation suite, providing deployment tracking, historical incident analysis, and release auditing logs.

#### Added
- Added `CHANGELOG.md` for recording chronological production deployments.
- Added `docs/deployment-history.md` documenting historical release deployments.
- Added `docs/incident-log.md` detailing root-cause analyses of past tagging-related production outages.
- Added `docs/release-notes-old.md` cataloging legacy release records.

---

## Release v1.0.0

- **Release Date:** 2026-06-06
- **Git Tag:** `v1.0.0` (Annotated)
- **Target Commit:** `ff1fb9a`
- **Rollback Target:** *None (Initial production release baseline)*

### Summary of Changes
Initial production baseline release establishing the Checkout Service repository scaffolding and core service entry point.

#### Added
- Created baseline repository layout with initial `README.md`.
- Implemented core placeholder entry point in `src/app.js` with basic startup logging.
- Created `src/README.md` defining service scope.
