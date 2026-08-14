# Cross-Platform Publishing Handbook & Toolchains

This directory contains production handbook guides, automated submission pipelines, visual asset generation workflows, metadata synchronization schemas, and release changelog formatting standards for **Cross-Platform Publishing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**—specifically Expo Application Services (EAS Build & EAS Submit), Fastlane multi-platform automation, and GitHub Actions CI/CD toolchains—it details how to orchestrate dual iOS and Android app store releases from a unified React Native codebase.

This guide is **not**:

- an authorization mechanism to bypass platform-specific app store review guidelines
- a substitute for verifying platform-specific build artifacts (`.ipa` for iOS, `.aab` for Android)
- a guide to managing separate, disconnected codebases for iOS and Android

---

# 1. Architecture of Cross-Platform Publishing

Cross-platform publishing unifies build compilation, secret management, asset generation, store metadata, and submission execution across both Apple App Store Connect and Google Play Console.

```text
┌────────────────────────────────────────────────────────┐
│             UNIFIED REACT NATIVE / EXPO CODEBASE       │
│  (Single JS/TS source + Config Plugins + App Config)   │
└──────────────────────────┬─────────────────────────────┘
                           │
             [ Automated CI/CD Toolchain ]
                           │
    ┌──────────────────────┴──────────────────────┐
    ▼                                             ▼
┌────────────────────────┐               ┌────────────────────────┐
│  EAS Build / Fastlane  │               │  EAS Build / Fastlane  │
│  iOS Pipeline (.ipa)   │               │  Android Pipeline (.aab)│
└───────────┬────────────┘               └───────────┬────────────┘
            │                                        │
            ▼ (App Store Connect API Key)            ▼ (GCP Service Account JSON)
┌────────────────────────┐               ┌────────────────────────┐
│ App Store Connect      │               │ Google Play Console    │
│ (TestFlight & Store)   │               │ (Internal & Production)│
└────────────────────────┘               └────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Purpose & Scope | Key Platform & Toolchain Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, cross-platform architecture, and universal submission rules. | High-level unified release flow, 2026 toolchain specifications. |
| **[submission.md](submission.md)** | Automated cross-platform submission workflows (EAS Submit, Fastlane, GitHub Actions). | Unified `eas.json` config, App Store Connect API keys, Play Service Accounts. |
| **[assets.md](assets.md)** | Visual asset generation pipelines (App Icons, Adaptive Icons, Splash Screens). | Expo Image Assets, 1024x1024 App Icon, 512x512 Android Adaptive Icon layer. |
| **[metadata.md](metadata.md)** | Cross-platform store metadata synchronization and unified schemas. | Shared metadata schemas, Fastlane Deliver & Supply synchronization. |
| **[release-notes.md](release-notes.md)** | Cross-platform release notes, git commit parsing, and changelog formatting. | Keep a Changelog standards, automated git commit parsing, dual store limits. |

---

# 3. Universal Cross-Platform Publishing Rules

All cross-platform publishing implementations in this playbook must adhere to five mandatory engineering rules:

```text
1. Single Source of Truth Configuration
   App versioning, bundle identifiers, display names, and asset paths MUST be managed from a
   unified configuration file (e.g., Expo `app.json` / `app.config.ts`) rather than duplicated
   in native Xcode and Android Studio projects.

2. Secret Isolation in CI/CD Secret Vaults
   App Store Connect API Keys (.p8) and Google Cloud Service Account JSON keys (.json) MUST be
   stored in managed CI secret vaults (EAS Secrets, GitHub Secrets). Never commit keys to git.

3. Automated Parallel Build Pipelines
   iOS (.ipa) and Android (.aab) production builds MUST be compiled in parallel CI/CD runners
   to ensure version alignment and prevent release skew between platforms.

4. Platform-Specific Compliance Verification
   Cross-platform deployment DOES NOT eliminate platform-specific compliance. iOS builds MUST
   comply with Privacy Manifests and Guideline 3.1.1; Android builds MUST target API Level 36.

5. Unified Staged Release Management
   Production releases MUST execute coordinated phased rollouts on iOS (Phased Release over 7 days)
   and Android (Staged Rollout starting at 1% or 5%) to maintain operational parity.
```

---

# 4. Related Repository Documentation

- [iOS Publishing Handbook](../ios/README.md) - App Store Connect publishing.
- [Android Publishing Handbook](../android/README.md) - Google Play Console publishing.
- [Release Engineering Subsystem](../../release-engineering/README.md) - CI/CD pipelines.

---

# 5. Official Sources

- Expo EAS Submit Documentation: https://docs.expo.dev/submit/introduction/
- Fastlane Multi-Platform Documentation: https://docs.fastlane.tools/

---

**Last verified:** August 14, 2026
