# Release Map

The Release Map establishes an immutable, single source of truth connecting Git tags to specific commit hashes, deployment dates, and shipping payloads. This document answers:
1. **What is currently running in production?**
2. **What previous known-good version do we roll back to in an emergency?**

---

## 1. Production Release Mapping Table

| Version (tag) | Commit (short hash) | Date | What Shipped | Deployment Status | Rollback Target |
|---|---|---|---|---|---|
| `v1.1.1` | `82535d9` | 2026-06-06 | Refactored dummy checkout service: added runtime `SERVICE_NAME` and `SERVICE_VERSION` identification and structured startup logging. | **Current Production** | `v1.1.0` |
| `v1.1.0` | `86989b6` | 2026-06-06 | Added operational documentation suite: structured changelog, historical release archive, and incident logs. | **Superseded (Known Good)** | `v1.0.0` |
| `v1.0.0` | `ff1fb9a` | 2026-06-06 | Initial checkout service baseline: core application scaffolding (`src/app.js`) and service placeholder. | **Baseline (Known Good)** | *None (Initial Release)* |

---

## 2. Emergency Rollback Traceability Procedure

When a production incident is detected on the current active release (`v1.1.1`), operations and SRE teams execute this deterministic recovery procedure:

### Step 1: List and Sort Release History
Run Git version sort to inspect the descending hierarchy of official releases:
```bash
git tag --sort=-v:refname
```
**Expected Output:**
```text
v1.1.1
v1.1.0
v1.0.0
```

### Step 2: Identify the Current Release and Rollback Target
- **Current Defective Release:** `v1.1.1` (top entry)
- **Last Known-Good Target:** `v1.1.0` (immediate predecessor)

### Step 3: Check Out the Last Known-Good Tag and Redeploy
Check out the immutable release tag directly:
```bash
git checkout v1.1.0
```
Trigger the deployment script or restart the service from this pinned ref:
```bash
# Example service startup / deployment validation
node src/app.js
```

### Reproducibility Rationale
Our new, consistent semantic tag history makes this rollback precise and reproducible because each tag points to an immutable, cryptographically verifiable Git commit with embedded metadata, removing all human ambiguity. In contrast to the original messy history—where tags like `latest-good`, `release_2`, and `v2-final-FINAL` lacked sequence, author attribution, and commit references—on-call engineers can now deterministically query the exact previous release and restore service without guesswork or fear of regression.
