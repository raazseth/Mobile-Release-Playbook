# Cross-Platform Publishing Handbook & Toolchains

This directory covers automated submission pipelines, visual asset generation, metadata synchronization, and release notes formatting for **Cross-Platform Publishing** in Expo and React Native applications — Expo Application Services (EAS Build & EAS Submit), Fastlane multi-platform automation, and GitHub Actions CI/CD, covering how to orchestrate iOS and Android store releases from a single codebase.

This guide is **not**:

- an authorization mechanism to bypass platform-specific app store review guidelines
- a substitute for verifying platform-specific build artifacts (`.ipa` for iOS, `.aab` for Android)
- a guide to managing separate, disconnected codebases for iOS and Android

---

# 1. Architecture of Cross-Platform Publishing

Cross-platform publishing unifies build compilation, secret management, asset generation, store metadata, and submission execution across both Apple App Store Connect and Google Play Console.

```text
Single React Native / Expo codebase (JS/TS + config plugins + app config)
        │
        ↓ (CI/CD toolchain)
        ├─→ EAS Build / Fastlane: iOS pipeline (.ipa)     ─→ App Store Connect API Key ─→ App Store Connect (TestFlight & Store)
        └─→ EAS Build / Fastlane: Android pipeline (.aab) ─→ GCP Service Account JSON  ─→ Google Play Console (Internal & Production)
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

Every guide in this directory assumes these five rules:

- [ ] **Single source of truth for configuration**: versioning, bundle identifiers, display names, and asset paths live in one config file (Expo `app.json` / `app.config.ts`), not duplicated across native Xcode and Android Studio projects.
- [ ] **Secrets isolated in CI secret vaults**: App Store Connect API Keys (`.p8`) and Google Cloud Service Account JSON keys are stored in EAS Secrets or GitHub Secrets — never committed to git.
- [ ] **iOS and Android builds run in parallel**, not sequentially, to keep both platforms on the same version and avoid release skew.
- [ ] **Cross-platform tooling doesn't remove platform-specific compliance**: iOS builds still need Privacy Manifests and Guideline 3.1.1 compliance; Android builds still need to meet the current target API level.
- [ ] **Rollouts are staged on both platforms**: iOS phased release over 7 days, Android staged rollout starting at 1% or 5% — not a direct-to-100% release on either store.

---

# Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/assets.md`
- `publishing/cross-platform/metadata.md`
- `publishing/cross-platform/release-notes.md`
- `publishing/cross-platform/submission.md`

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Checklists

- `checklists/cross-platform.md`

### Store operations

- `store-operations/README.md`

---

# Official sources

- Expo EAS Submit Documentation: https://docs.expo.dev/submit/introduction/
- Fastlane Multi-Platform Documentation: https://docs.fastlane.tools/

---

**Last verified:** August 14, 2026

