# CI/CD Release Pipeline Architecture Handbook

This handbook details branching strategies (`main` -> `release/v1.2.0`), tag triggers (`v1.2.0`), matrix build pipelines, automated promotion gates, and release candidate tracking for **Release Pipelines** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to structure automated release pipelines.

This guide is **not**:

- an authorization mechanism to merge un-tested feature branches directly to `main`
- a substitute for establishing automated testing quality gates in CI
- a guide to executing manual build steps on local developer machines

---

# 1. CI/CD Release Pipeline Architecture

The automated release pipeline orchestrates source code verification, testing, binary compilation, and store submission.

```text
┌────────────────────────────────────────────────────────┐
│             CI/CD RELEASE PIPELINE STAGES              │
│                                                        │
│  Stage 1: Source Control & Trigger (`v1.2.0` Tag)      │
│  Stage 2: Dependency Install & Cache Restoration       │
│  Stage 3: Automated Test Execution (Jest + Maestro)    │
│  Stage 4: Parallel Matrix Build (iOS `.ipa` + Android `.aab`)│
│  Stage 5: Automated Pre-Release Quality Audits         │
│  Stage 6: Store Submission (TestFlight & Play Closed)  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Candidate Promoted to Store Review Track ]
```

---

# 2. Release Branching & Tagging Strategy

- **Release Branch**: Cut branch `release/v1.2.0` from `main` 7 days prior to target release date.
- **Git Release Tag**: Tag release candidate commit with SemVer format: `v1.2.0-rc.1` (Release Candidate) and `v1.2.0` (Production Tag).
- **Tag Trigger**: Pushing a tag matching `v*.*.*` triggers the automated CI/CD release pipeline.

---

# 3. Operational Verification Checklist

- [ ] **Release Tag Trigger Active**: CI pipeline triggers on `v*.*.*` tag push.
- [ ] **Parallel Matrix Builds**: iOS and Android builds execute concurrently in CI pipeline.
- [ ] **Promotion Gates Configured**: Store upload occurs ONLY after 100% test pass.

---

# 4. Official Sources

- Git Release Workflows: https://git-scm.com/book/en/v2/Git-Basics-Tagging

---

**Last verified:** August 14, 2026

---

# Related documentation

### Release engineering

- `release-engineering/README.md`
- `release-engineering/app-store-connect-api.md`
- `release-engineering/build-systems.md`
- `release-engineering/environment-management.md`
- `release-engineering/google-play-api.md`
- `release-engineering/release-configurations.md`

### EAS

- `release-engineering/eas/README.md`

### Fastlane

- `release-engineering/fastlane/README.md`

### GitHub Actions

- `release-engineering/github-actions/README.md`

### Signing

- `signing/README.md`

### AI orchestration

- `ai/orchestration/mcp.md`
- `ai/orchestration/tool-permissions.md`
