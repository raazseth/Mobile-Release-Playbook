# Cross-Platform Publishing

Most teams don't publish iOS and Android as two separate manual workflows — they drive both from one pipeline. This directory covers the tooling that does that: EAS Build and EAS Submit, Fastlane running against both platforms, and GitHub Actions CI/CD, all working from a single React Native or Expo codebase.

This guide is **not**:

- an authorization mechanism to bypass either platform's app store review guidelines
- a substitute for verifying the platform-specific build artifacts (`.ipa` for iOS, `.aab` for Android)
- a guide to running iOS and Android as separate, disconnected codebases

---

## 1. How the pieces fit together

```text
Single React Native / Expo codebase (JS/TS + config plugins + app config)
        │
        ↓ (CI/CD toolchain)
        ├─→ EAS Build / Fastlane: iOS pipeline (.ipa)     ─→ App Store Connect API Key ─→ App Store Connect (TestFlight & Store)
        └─→ EAS Build / Fastlane: Android pipeline (.aab) ─→ GCP Service Account JSON  ─→ Google Play Console (Internal & Production)
```

## 2. What's in this directory

| Guide | Covers |
|---|---|
| [submission.md](submission.md) | Automated submission with EAS Submit, Fastlane, and GitHub Actions |
| [assets.md](assets.md) | App icons, adaptive icons, and splash screens from a single source |
| [metadata.md](metadata.md) | Keeping one metadata schema in sync across both stores |
| [release-notes.md](release-notes.md) | Writing and generating release notes for both stores' character limits |

The platform-specific docs — [`../ios/`](../ios/README.md) and [`../android/`](../android/README.md) — are still the reference for what this tooling is actually doing on each store. Use this directory to automate the workflow; use those to understand or debug it.

## 3. Rules that apply to every guide in this directory

- **One config file, not three.** Versioning, bundle identifiers, display names, and asset paths live in `app.json` / `app.config.ts` — not duplicated across native Xcode and Android Studio projects.
- **Secrets stay in a secret vault.** App Store Connect API Keys (`.p8`) and Google Cloud Service Account JSON keys live in EAS Secrets or GitHub Secrets, never in git.
- **Build both platforms in parallel, not sequentially,** so you don't end up with iOS and Android drifting to different versions.
- **Cross-platform tooling doesn't remove platform-specific compliance.** iOS still needs Privacy Manifests and Guideline 3.1.1 compliance; Android still needs to meet the current target API level.
- **Stage rollouts on both platforms.** iOS phased release over 7 days, Android staged rollout starting at 1% or 5% — never a direct-to-100% release on either store.

---

## Official sources

- Expo EAS Submit documentation: https://docs.expo.dev/submit/introduction/
- Fastlane multi-platform documentation: https://docs.fastlane.tools/

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/assets.md`
- `publishing/cross-platform/metadata.md`
- `publishing/cross-platform/release-notes.md`
- `publishing/cross-platform/submission.md`

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Release engineering

- `release-engineering/eas/README.md`

### Checklists

- `checklists/cross-platform.md`

### Store operations

- `store-operations/README.md`
