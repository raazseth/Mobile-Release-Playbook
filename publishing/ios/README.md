# iOS Publishing Handbook & App Store Administration

This directory covers build archiving, TestFlight beta distribution, App Store Review preparation, and phased release rollout for **iOS App Publishing** in Expo and React Native applications — App Store Connect API Key (`.p8`) authentication, `ITSAppUsesNonExemptEncryption` declarations, TestFlight Beta App Review rules, and the 7-day phased release schedule, covering how to compile, test, stage, and publish iOS applications on the App Store.

This guide is **not**:

- an authorization mechanism to submit un-tested iOS builds directly to App Store Review
- a substitute for declaring Privacy Manifests (`PrivacyInfo.xcprivacy`) in compiled binaries
- a guide to using deprecated Xcode upload tools (`altool` is deprecated in favor of App Store Connect API and Transporter)

---

# 1. Architecture of iOS Release Pipelines

iOS application publishing follows a structured progression from local `.ipa` compilation through TestFlight beta testing channels to App Store production releases.

```text
eas build → signed .ipa (Distribution Certificate & Profile)
        │
        ↓ (App Store Connect API Key .p8)
TestFlight beta distribution
  - Internal testing: instant access, up to 100 testers
  - External testing: Beta App Review required, up to 10,000 testers
        │
        ↓
App Store production release
  - App Store Review (Guidelines 2.1, 3.1.1)
  - Phased release over 7 days (Day 1: 1% → Day 7: 100%)
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

Every guide in this directory assumes these five rules:

- [ ] **Export compliance declared**: `Info.plist` includes `ITSAppUsesNonExemptEncryption` (set to `false` if the app only uses standard HTTPS encryption) — otherwise export compliance prompts block TestFlight processing.
- [ ] **App Store Connect API Key authentication**: automated tools (EAS Submit, Fastlane Deliver) authenticate with an App Store Connect API Key (`.p8`), not password-based auth or the deprecated `altool`.
- [ ] **Demo credentials provided for review**: if the app requires login, a working demo username/password is in App Store Connect's Reviewer Notes, and the demo account stays active for the duration of review.
- [ ] **Phased release enabled**: production updates use Phased Release Over 7 Days (Day 1: 1%, Day 2: 2%, Day 3: 5%, Day 4: 10%, Day 5: 20%, Day 6: 50%, Day 7: 100%) rather than releasing to 100% immediately.
- [ ] **No alpha channel in screenshots**: PNG assets uploaded to App Store Connect have alpha transparency stripped — an active alpha channel triggers an upload rejection.

---

# Related documentation

### Publishing (iOS)

- `publishing/ios/app-review.md`
- `publishing/ios/app-store-connect.md`
- `publishing/ios/build-upload.md`
- `publishing/ios/metadata.md`
- `publishing/ios/production-release.md`
- `publishing/ios/screenshots.md`
- `publishing/ios/testflight.md`

### Store accounts

- `store-accounts/app-store-connect.md`

### iOS signing

- `signing/ios/README.md`
- `signing/ios/distribution.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/ios.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Apple App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- TestFlight Overview: https://developer.apple.com/testflight/
- Apple App Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

---

**Last verified:** August 14, 2026

