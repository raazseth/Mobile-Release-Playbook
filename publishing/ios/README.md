# iOS Publishing Handbook & App Store Administration

This directory contains production handbook guides, build archiving standards, TestFlight beta distribution workflows, App Store Review preparation rules, and Phased Release protocols for **iOS App Publishing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**—specifically App Store Connect API Key (`.p8`) authentication, `ITSAppUsesNonExemptEncryption` declarations, TestFlight Beta App Review rules, and 7-Day Phased Releases—it details how to compile, test, stage, and publish iOS applications on the Apple App Store.

This guide is **not**:

- an authorization mechanism to submit un-tested iOS builds directly to App Store Review
- a substitute for declaring Privacy Manifests (`PrivacyInfo.xcprivacy`) in compiled binaries
- a guide to using deprecated Xcode upload tools (`altool` is deprecated in favor of App Store Connect API and Transporter)

---

# 1. Architecture of iOS Release Pipelines

iOS application publishing follows a structured progression from local `.ipa` compilation through TestFlight beta testing channels to App Store production releases.

```text
┌────────────────────────────────────────────────────────┐
│             iOS BUILD COMPILATION (`eas build`)        │
│  - React Native Hermes JS + Native Objective-C/Swift   │
│  - Signed with Distribution Certificate & Profile     │
│  - Generates Production `.ipa` Archive                 │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼ (App Store Connect API Key `.p8`)
┌────────────────────────────────────────────────────────┐
│             TESTFLIGHT BETA DISTRIBUTION               │
│                                                        │
│  1. Internal Testing (Instant access; 100 testers)    │
│  2. External Testing (Beta App Review; 10,000 testers)│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             APP STORE PRODUCTION RELEASE               │
│  - App Store Review Inspection (Guidelines 2.1, 3.1.1) │
│  - Phased Release Over 7 Days (Day 1: 1% → Day 7: 100%)│
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Purpose & Scope | Key Platform & Store Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, iOS release architecture, and universal publishing rules. | High-level iOS release pipeline, 2026 platform rules. |
| **[build-upload.md](build-upload.md)** | iOS build compilation (`.ipa`), Transporter CLI, and App Store Connect API keys. | `.ipa` archiving, ASC API key (`.p8`), `xcrun notarytool` / Transporter CLI. |
| **[app-store-connect.md](app-store-connect.md)** | App Store Connect build management, version mapping, and export compliance. | Version string mapping, build number incrementing, `ITSAppUsesNonExemptEncryption`. |
| **[testflight.md](testflight.md)** | TestFlight Internal (100 testers) vs External (10,000 testers) beta distribution tracks. | Internal testing (no review), External testing (Beta App Review required). |
| **[production-release.md](production-release.md)** | App Store production releases and Phased Release Over 7 Days. | Phased Release schedule (Day 1: 1% → Day 7: 100%), pausing phased releases. |
| **[app-review.md](app-review.md)** | App Store Review policies, demo account requirements, and reviewer notes. | Guideline 2.1 (completeness), Guideline 3.1.1 (IAP), Guideline 5.1.1 (privacy). |
| **[metadata.md](metadata.md)** | App Store Connect text metadata (Name 30c, Subtitle 30c, Keywords 100c, Desc 4000c). | Name 30c, Subtitle 30c, Keywords 100c (no spaces), Promotional Text 170c. |
| **[screenshots.md](screenshots.md)** | iPhone 6.9" Super Retina XDR (1320x2868), iPad 13" (2064x2752), no alpha channels. | Canonical 6.9" display class (1320x2868 px), no alpha transparency (`ITMS-90032`). |

---

# 3. Universal iOS Publishing Rules

All iOS publishing implementations in this playbook must adhere to five mandatory engineering rules:

```text
1. Export Compliance Declaration in `Info.plist`
   All iOS binaries MUST include `ITSAppUsesNonExemptEncryption` in `Info.plist` (set to `false` if
   using standard HTTPS encryption) to prevent automated export compliance halts on TestFlight.

2. App Store Connect API Key (`.p8`) Authentication
   Automated build deployment tools (EAS Submit, Fastlane Deliver) MUST authenticate using App Store Connect
   API Keys (.p8). Deprecated password-based authentication or legacy `altool` commands ARE FORBIDDEN.

3. Mandatory Demo Credentials for Review
   If your app requires user authentication, functional demo credentials (username + password) MUST be
   provided in App Store Connect Reviewer Notes. Demo accounts MUST remain active during review.

4. 7-Day Phased Release for Production Updates
   All production version updates MUST enable Phased Release Over 7 Days (Day 1: 1%, Day 2: 2%, Day 3: 5%,
   Day 4: 10%, Day 5: 20%, Day 6: 50%, Day 7: 100%) to mitigate unforeseen production crash spikes.

5. Strict Alpha Transparency Exclusion
   All screenshot assets uploaded to App Store Connect MUST have alpha transparency channels stripped.
   Uploading PNG assets with an active alpha channel will trigger an instant `ITMS-90032` rejection.
```

---

# 4. Related Repository Documentation

- [Android Publishing Handbook](../android/README.md) - Google Play publishing.
- [Cross-Platform Publishing Handbook](../cross-platform/README.md) - Unified EAS/Fastlane releases.
- [Signing iOS Handbook](../../signing/ios/certificates.md) - Certificates & provisioning profiles.

---

# 5. Official Sources

- Apple App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- TestFlight Overview: https://developer.apple.com/testflight/
- Apple App Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

---

**Last verified:** August 14, 2026
