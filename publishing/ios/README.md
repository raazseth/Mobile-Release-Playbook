# iOS Publishing

This directory covers building and archiving an `.ipa`, distributing it through TestFlight, getting through App Store Review, and rolling a release out to users with a phased release. It's the practical path from a signed build to a live App Store listing.

This guide is **not**:

- an authorization mechanism to submit an un-tested build straight to App Store Review
- a substitute for declaring a Privacy Manifest (`PrivacyInfo.xcprivacy`) in your compiled binary
- a guide to using deprecated Xcode upload tools — `altool` is deprecated in favor of the App Store Connect API and Transporter

---

## 1. The pipeline, end to end

```text
eas build → signed .ipa (Distribution Certificate & Provisioning Profile)
        │
        ↓ (App Store Connect API Key, .p8)
TestFlight beta distribution
  - Internal testing: instant access, up to 100 testers
  - External testing: Beta App Review required, up to 10,000 testers
        │
        ↓
App Store production release
  - App Store Review (Guidelines 2.1, 3.1.1, and others)
  - Phased release over 7 days (Day 1: 1% → Day 7: 100%)
```

## 2. What's in this directory

| Guide | Covers |
|---|---|
| [build-upload.md](build-upload.md) | Compiling the `.ipa`, uploading it via Transporter/Fastlane, App Store Connect API keys |
| [app-store-connect.md](app-store-connect.md) | Version mapping (`CFBundleShortVersionString` vs `CFBundleVersion`), export compliance, attaching a build to a release |
| [testflight.md](testflight.md) | Internal vs. external testing, Beta App Review, build expiration |
| [production-release.md](production-release.md) | Release triggers, phased release, pausing a rollout |
| [app-review.md](app-review.md) | The App Store Review guidelines that most often cause rejections, demo accounts, reviewer notes |
| [metadata.md](metadata.md) | App Store Connect text fields and character limits |
| [screenshots.md](screenshots.md) | Screenshot and app icon specs by device class |

## 3. Rules that apply to every guide in this directory

- **Declare export compliance.** `Info.plist` needs `ITSAppUsesNonExemptEncryption` (set to `false` if the app only uses standard HTTPS) — without it, export compliance prompts block TestFlight processing.
- **Authenticate with an App Store Connect API Key.** Automated tools (EAS Submit, Fastlane Deliver) should use a `.p8` key, not password-based auth or the deprecated `altool`.
- **Give reviewers a working demo account.** If the app requires login, put a working username/password in App Store Connect's Reviewer Notes, and keep the account active for the whole review.
- **Use phased release on every production update.** Don't push straight to 100% — see [production-release.md](production-release.md) for the schedule.
- **Strip alpha channels from screenshots.** App Store Connect rejects PNGs with transparency baked in.

---

## Official sources

- Apple App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- TestFlight Overview: https://developer.apple.com/testflight/
- Apple App Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

**Last verified:** August 14, 2026

---

## Related documentation

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
