# Publishing & Store Distribution Subsystem Handbook

This directory covers store submission procedures, metadata packaging, asset validation rules, and release automation pipelines for **Publishing** in Expo and React Native applications — the one-time-per-release mechanics of getting a build from a signed binary to a live store listing. It details how to deploy mobile app binaries to the Apple App Store, Google Play Store, and cross-platform distribution services.

This guide is **not**:

- an authorization mechanism to upload un-audited build artifacts to production store tracks
- a substitute for verifying platform-specific submission guidelines
- a guide to bypassing store review requirements

---

# 1. Architecture of Mobile Publishing

Publishing coordinates binary packaging, metadata assembly, asset validation, and store API delivery across iOS and Android platforms.

```text
Signed build artifact (.ipa / .aab)
        │
        ↓
[ ios/ ]              App Store Connect, TestFlight, .ipa uploads, phased releases
        │
        ↓
[ android/ ]           Google Play Console, Play App Signing, .aab bundles, staged rollouts
        │
        ↓
[ cross-platform/ ]    EAS Submit / Fastlane pipelines spanning both stores, shared assets
        │
        ↓
Live store listing
```

---

# 2. Subsystem Directory Taxonomy

| Publishing Subsystem | Core Distribution Focus | Key Guides & Tools |
|---|---|---|
| **[README.md](README.md)** | Subsystem parent index and publishing architecture. | High-level distribution overview. |
| **[ios/](ios/README.md)** | iOS App Store distribution, TestFlight, `.ipa` archiving. | [build-upload.md](ios/build-upload.md), [testflight.md](ios/testflight.md), [screenshots.md](ios/screenshots.md). |
| **[android/](android/README.md)** | Google Play Store distribution, `.aab` bundles, Staged Rollouts. | [app-bundle.md](android/app-bundle.md), [play-console.md](android/play-console.md), [screenshots.md](android/screenshots.md). |
| **[cross-platform/](cross-platform/README.md)** | Multi-platform deployment, EAS Submit, Fastlane automation. | [submission.md](cross-platform/submission.md), [assets.md](cross-platform/assets.md), [metadata.md](cross-platform/metadata.md). |

---

# 3. Operational Verification Checklist

- [ ] **Target Platform Selected**: Publishing pathway chosen (iOS, Android, or Cross-Platform).
- [ ] **Binary Upload Verified**: `.ipa` or `.aab` uploaded cleanly to store processing queue.
- [ ] **Metadata & Assets Validated**: Screenshots, app icons, and descriptions meet store guidelines.

---

# 4. Official Sources

- Apple App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

### Store operations

- `store-operations/README.md`
