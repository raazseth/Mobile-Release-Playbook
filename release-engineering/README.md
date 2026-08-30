# Release Engineering & CI/CD Infrastructure Handbook

This directory contains production handbook guides, build system configurations, environment isolation rules, Expo Application Services (EAS) pipelines, Fastlane automation suites, and GitHub Actions CI/CD matrix workflows for **Release Engineering** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to build, test, archive, and automate mobile application delivery to store platforms.

This guide is **not**:

- an authorization mechanism to execute un-tested manual builds directly from developer workstations to production store tracks
- a substitute for establishing automated CI/CD pipeline quality gates
- a guide to storing un-encrypted API keys in build scripts

---

# 1. Architecture of Mobile Release Engineering

Release engineering automates the transition of source code into signed, audited, and store-ready binary artifacts (`.ipa` and `.aab`).

```text
┌────────────────────────────────────────────────────────┐
│             RELEASE ENGINEERING PIPELINE               │
│                                                        │
│  [ Git Commit / Tag Push (`v1.2.0`) ]                 │
│                         │                              │
│                         ▼                              │
│  [ CI/CD Pipeline Trigger (GitHub Actions / EAS) ]     │
│    - Install & Cache Dependencies                      │
│    - Run Unit & Integration Tests                      │
│    - Execute Pre-Release Audits                        │
│                         │                              │
│                         ▼                              │
│  [ Native Binary Packaging (Xcode Archive / Gradle) ]  │
│    - Code Signing via Fastlane Match / Keystore        │
│    - R8 Minification & Hermes Bytecode Compilation    │
│                         │                              │
│                         ▼                              │
│  [ Store Upload Automation (EAS Submit / Fastlane) ]   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Release Candidate on TestFlight & Play Closed Testing ]
```

---

# 2. Subsystem Directory Taxonomy

| Release Subsystem | Primary Focus & Domain Scope | Key Components & Files |
|---|---|---|
| **[README.md](README.md)** | Subsystem root index, release architecture, and governance rules. | Master taxonomy and pipeline rules. |
| **Core Architecture** | Build systems, release configs, env isolation, and API automation. | [build-systems.md](build-systems.md), [release-configurations.md](release-configurations.md), [environment-management.md](environment-management.md), [release-pipelines.md](release-pipelines.md), [app-store-connect-api.md](app-store-connect-api.md), [google-play-api.md](google-play-api.md). |
| **[eas/](eas/README.md)** | Expo Application Services (EAS Build, EAS Submit, EAS Update). | [eas-build.md](eas/eas-build.md), [eas-submit.md](eas/eas-submit.md), [eas-update.md](eas/eas-update.md). |
| **[fastlane/](fastlane/README.md)** | Fastlane automation lanes, Appfile, Matchfile, Pilot, Supply. | [fastfile.md](fastlane/fastfile.md), [appfile.md](fastlane/appfile.md), [matchfile.md](fastlane/matchfile.md). |
| **[github-actions/](github-actions/README.md)** | GitHub Actions CI/CD workflows, matrix builds, action templates. | [workflows.md](github-actions/workflows.md), [action-templates.md](github-actions/action-templates.md). |

---

# 3. Universal Release Engineering Governance Rules

All release engineering implementations in this playbook must adhere to five mandatory governance rules:

```text
1. Automated CI/CD Execution Mandate
   Production releases MUST be compiled and packaged exclusively by automated CI/CD runners
   (EAS Build or GitHub Actions). Local developer workstation builds are FORBIDDEN for store releases.

2. Reproducible Build Configuration
   All build dependencies MUST be pinned to exact versions in `package-lock.json` / `bun.lockb`,
   `Podfile.lock`, and `build.gradle` to guarantee 100% build reproducibility.

3. Strict Environment Isolation
   Development, Staging, and Production environment configurations, API endpoints, and bundle IDs
   MUST be strictly separated and validated at build time.

4. Immutability of Release Candidates
   Once a release candidate binary is compiled and audited, that EXACT binary MUST be promoted
   through testing tracks to production without re-compiling source code.

5. Zero Plain-Text Credentials in Build Configs
   App Store Connect API keys, Google Play Service Account JSONs, and keystore passwords MUST be
   injected dynamically from secret managers via environment variables.
```

---

# 4. Related Repository Documentation

- [Code Signing Subsystem](../signing/README.md) - Certificates & Keystores.
- [Pre-Release Quality Gates](../pre-release/README.md) - Release readiness audits.
- [iOS Publishing Handbook](../publishing/ios/README.md) - iOS distribution.

---

# 5. Official Sources

- Expo EAS Documentation: https://docs.expo.dev/eas/
- Fastlane Documentation: https://docs.fastlane.tools/

---

**Last verified:** August 14, 2026

