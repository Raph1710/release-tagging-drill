# Release and Rollback Traceability Guide

This guide demonstrates the step-by-step procedure for performing a fast, deterministic, and traceable rollback in production using our standardized semantic tag history.

---

## 1. Context: Production Incident on Active Release

Assume the latest production release `v1.1.1` has just been deployed and triggers unforeseen application errors or performance degradation. Operations requires an immediate rollback to the last verified, stable build.

---

## 2. Emergency Rollback Step-by-Step

### Step 1: Inspect Sortable Release History
Run the Git version-sort command to view all releases ordered deterministically from newest to oldest:

```bash
git tag --sort=-v:refname
```

**Output:**
```text
v1.1.1
v1.1.0
v1.0.0
```

Because tags adhere strictly to SemVer (`vMAJOR.MINOR.PATCH`), the top entry `v1.1.1` is guaranteed to be the current release, and the subsequent entry `v1.1.0` is the immediate previous release.

---

### Step 2: Roll Back to the Last Known-Good Tag
Execute checkout directly to the target tag ref:

```bash
git checkout v1.1.0
```

*Note:* Git will switch to a detached HEAD state at the exact commit (`86989b6`) tagged by `v1.1.0`.

---

### Step 3: Trigger Redeployment / Validate Service
Re-run or restart the service from this pinned commit:

```bash
node src/app.js
```

---

## 3. Why Our New Tag History Ensures Precision and Reproducibility

Our new, consistent semantic tag history makes this rollback precise and reproducible because every release is backed by an annotated Git tag containing immutable commit pointers, cryptographic tagger identity, timestamps, and release descriptions. In the original chaotic history, teams relied on ambiguous, mutable names like `latest-good`, `release_2`, or `v2-final-FINAL`, which lacked commit metadata and caused catastrophic rollback failures (such as the 502 outage in Incident 1). With strict SemVer and annotated tags, operations can reliably identify, verify, and restore the exact prior known-good state within seconds, eliminating all guesswork.
