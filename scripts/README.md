# Release Automation Scripts & CLI Tooling Subsystem Handbook

This directory contains production handbook guides, CLI execution scripts, version bumping automation, build packaging validators, store metadata compliance checkers, API connection verifiers, and pre-release quality gate audit scripts for **Release Automation Scripts** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to automate pre-flight validation checks before submitting builds to store platforms.

This guide is **not**:

- an authorization mechanism to execute un-tested CLI scripts against production store accounts without dry-run validation
- a substitute for running automated unit and integration tests in CI/CD pipelines
- a guide to bypassing store policy validation checks

---

# 1. Architecture of Release Automation Tooling

Release automation scripts validate source code, app metadata, build artifacts, and store API connectivity prior to production submission.

```text
┌────────────────────────────────────────────────────────┐
│             RELEASE AUTOMATION CLI PIPELINE            │
│                                                        │
│  Stage 1: Version Bumping & SemVer Sync                │
│           (`version-bump.js` ──→ `app.json`, `plist`)  │
│                         │                              │
│                         ▼                              │
│  Stage 2: Metadata & Policy Compliance Validator       │
│           (`validate-metadata.js` ──→ Guideline 2.3)   │
│                         │                              │
│                         ▼                              │
│  Stage 3: Build Artifact & Asset Inspector             │
│           (`validate-build.js` ──→ `.ipa` / `.aab`)    │
│                         │                              │
│                         ▼                              │
│  Stage 4: Pre-Release Quality & Privacy Audit Gate     │
│           (`audit-release.js` ──→ Target SDK 36)       │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Clean Report Passed ──→ Proceed to Store Submit ]
```

---

# 2. Subsystem Directory Taxonomy

| Script Tooling Category | Primary Focus & Domain Scope | Key Commands & Files |
|---|---|---|
| **[README.md](README.md)** | Subsystem root index, automation architecture, and governance rules. | Master CLI taxonomy. |
| **[version-bump/](version-bump/README.md)** | Automated version bumping across `package.json`, `app.json`, `Info.plist`, `build.gradle`. | [version-bump.md](version-bump/version-bump.md). |
| **[build-validation/](build-validation/README.md)** | Binary packaging inspection (`.ipa`/`.aab`), R8 minification, Hermes bytecode, assets. | [validate-build.md](build-validation/validate-build.md). |
| **[metadata-validation/](metadata-validation/README.md)** | Store metadata character limits, Guideline 2.3.7 competitor brand terms, URLs. | [validate-metadata.md](metadata-validation/validate-metadata.md). |
| **[store-validation/](store-validation/README.md)** | App Store Connect API and Google Play API credentials and connectivity verification. | [validate-store.md](store-validation/validate-store.md). |
| **[release-audit/](release-audit/README.md)** | OWASP security audit, dependency scan (`npm audit`), Target SDK 36, Privacy Manifest. | [audit-release.md](release-audit/audit-release.md). |

---

# 3. Universal Automation Governance Rules

All automation scripts in this repository MUST comply with five mandatory governance rules:

```text
1. Zero Non-Zero Exit Code Masking
   All validation scripts MUST return non-zero exit codes (`exit 1`) upon encountering P0 or P1
   compliance failures, ensuring CI/CD pipelines halt immediately.

2. Mandatory Dry-Run Execution Mode
   Every script modifying files or store records MUST support a `--dry-run` flag allowing engineers
   to preview actions without committing changes to disk or remote servers.

3. Complete Cross-Platform Compatibility
   Automation scripts MUST run seamlessly across macOS (Apple Silicon / Intel), Linux (Ubuntu CI runners),
   and Windows (PowerShell / WSL) without OS-specific path failures.

4. Secret-Safe Execution Logging
   Validation scripts MUST NEVER output raw API keys, bearer tokens, or keystore passwords in console
   logs or report outputs.

5. Strict 2026 Policy Alignment
   Validators MUST enforce 2026 store rules (Android Target API 36, 6.9" display class screenshots,
   and Apple `PrivacyInfo.xcprivacy` manifest requirements).
```

---

# 4. Related Repository Documentation

- [Release Engineering Subsystem](../release-engineering/README.md) - Build & CI/CD.
- [Pre-Release Quality Audit Handbooks](../pre-release/README.md) - GO/NO-GO audits.
- [Store Operations Handbooks](../store-operations/README.md) - Metadata limits.

---

# 5. Official Sources

- Node.js CLI Scripting Documentation: https://nodejs.org/docs/latest/api/cli.html

---

**Last verified:** August 14, 2026

