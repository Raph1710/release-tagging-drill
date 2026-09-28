# Software Versioning and Tagging Convention

This document defines the formal versioning and Git tagging policy for the Checkout Service engineering team. All production, staging, and pre-release software deliverables must adhere strictly to these rules.

---

## 1. Semantic Versioning Rules (SemVer 2.0.0)

Versions follow the standard format:
$$\text{MAJOR}.\text{MINOR}.\text{PATCH}$$

Each segment carries explicit semantic meaning regarding compatibility and functionality:

### 1.1 MAJOR Version
- **Definition:** Incremented when backwards-incompatible API changes, breaking architectural refactors, database schema alterations requiring manual data migration, or removed endpoints are introduced.
- **When to Bump:** Consumers or downstream dependencies must update their integration code to remain compatible with this release.
- **Concrete Example Bump:**
  - `v1.4.2` $\rightarrow$ `v2.0.0`
  - *Context:* Upgrading the payment processing interface from synchronous REST endpoints to an asynchronous tokenized webhook architecture, breaking backwards compatibility for existing checkout clients.

### 1.2 MINOR Version
- **Definition:** Incremented when backwards-compatible functionality is added to the codebase.
- **When to Bump:** New features, endpoints, or optional parameters are introduced without breaking existing client workflows. Minor versions reset the `PATCH` counter to `0`.
- **Concrete Example Bump:**
  - `v1.0.0` $\rightarrow$ `v1.1.0`
  - *Context:* Introducing payment token caching and audit logging while maintaining full backwards compatibility for existing checkout requests.

### 1.3 PATCH Version
- **Definition:** Incremented when backwards-compatible bug fixes, security patches, or minor performance optimizations are applied.
- **When to Bump:** Defect resolution that does not introduce new functionality or alter public APIs.
- **Concrete Example Bump:**
  - `v1.1.0` $\rightarrow$ `v1.1.1`
  - *Context:* Resolving an edge-case checkout button timeout and handling null responses during payment provider transient failures.

---

## 2. Tag Naming Format

All Git release tags must strictly adhere to the following standard schema:

### 2.1 The Pattern
```text
v<MAJOR>.<MINOR>.<PATCH>
```

- **Prefix:** Must begin with a lowercase `v` (e.g., `v1.0.0`, NOT `V1.0.0`, `version-1.0`, or `1.0.0`).
- **Structure:** Followed immediately by three non-negative integers separated by dots: `<MAJOR>.<MINOR>.<PATCH>`.
- **Regex Enforcement:**
  ```regex
  ^v(0|[1-9]\d*)\.(0|[1-9]\d*)\.(0|[1-9]\d*)$
  ```

### 2.2 Concrete Example
- **Standard Tag:** `v1.1.0`
- **Invalid Variations (Prohibited):**
  - `1.1.0` (missing leading `v` prefix)
  - `version-1.1.0` (non-standard prefix)
  - `v1.1` (missing patch version)
  - `release_1.1.0` (snake_case keyword prohibited)
  - `v1.1.0-final` (informal ad-hoc suffixes prohibited)

---

## 3. Annotated vs. Lightweight Tags

### 3.1 Policy: Mandatory Annotated Tags for Releases
The team **exclusively uses annotated tags (`git tag -a`)** for all production and formal environment releases. Lightweight tags are strictly forbidden for deployment artifacts.

### 3.2 Rationale
1. **Full Object Integrity:** An annotated tag is stored as an independent, immutable object in the Git object database. It records the tagger's name, email, and creation timestamp.
2. **Accountability and Cryptographic Auditing:** Annotated tags allow verification of who authorized and cut the release. They also support cryptographic signing (`git tag -s` with GPG/SSH keys) to guarantee authenticity.
3. **Embedded Release Message:** Annotated tags contain an explicit multi-line release summary explaining what shipped.
4. **Distinction from Lightweight Tags:** A lightweight tag is merely a floating pointer to a commit hash (identical to a branch that does not move). Lightweight tags lack metadata, author identity, timestamp, and release notes, which caused the rollback failures documented in Incident 1.

### 3.3 Exact Command
To create an annotated release tag, use:
```bash
git tag -a v1.1.0 -m "Release 1.1.0: Add payment token caching and timeout resilience"
```
To verify the annotated tag metadata:
```bash
git show v1.1.0
```

---

## 4. Pre-Release Rule

### 4.1 Tag Format for Pre-Releases
When releasing alpha builds, beta builds, or Release Candidates (RCs) for testing and staging promotion, a hyphen and dot-separated identifier are appended:

```text
v<MAJOR>.<MINOR>.<PATCH>-<identifier>.<iteration>
```

Approved identifiers: `alpha`, `beta`, `rc`

#### Examples:
- `v1.5.0-alpha.1` (Early internal testing)
- `v1.5.0-beta.1` (Feature complete, wider integration testing)
- `v1.5.0-rc.1` (Release candidate nominated for production deployment)
- `v1.5.0-rc.2` (Second candidate resolving defect found in RC1)

### 4.2 Ordering Relative to Final Release
In accordance with Semantic Versioning 2.0.0 and Git version sorting (`git tag --sort=-v:refname`):
- **Rule:** A pre-release version has a **lower precedence** than the associated normal release.
- **Comparison Hierarchy:**
  $$\text{v1.5.0-alpha.1} < \text{v1.5.0-beta.1} < \text{v1.5.0-rc.1} < \text{v1.5.0-rc.2} < \text{v1.5.0}$$

When sorting via `git tag --sort=-v:refname`, the final production release `v1.5.0` correctly appears at the top:
```text
v1.5.0
v1.5.0-rc.2
v1.5.0-rc.1
v1.5.0-beta.1
```
This ensures CI/CD automation and operations teams will never accidentally promote a pre-release candidate over the certified production release.
