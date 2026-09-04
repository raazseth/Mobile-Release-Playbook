# Publishing

Publishing covers the mechanics of getting a signed build from your machine (or CI) onto the App Store and Google Play — uploading the binary, filling in store metadata, and rolling it out to users. It's the one-time-per-release work, as opposed to `store-operations/`, which covers what you do to a listing for as long as the app exists.

This guide is **not**:

- an authorization mechanism to upload an un-audited build to a production store track
- a substitute for reading the platform-specific submission guidelines linked throughout this directory
- a guide to bypassing store review

---

## 1. How a release moves through this directory

A release starts as a signed build artifact and ends as a live store listing:

```text
Signed build artifact (.ipa / .aab)
        │
        ↓
ios/              App Store Connect, TestFlight, .ipa uploads, phased release
        │
        ↓
android/          Google Play Console, Play App Signing, .aab bundles, staged rollout
        │
        ↓
cross-platform/   EAS Submit / Fastlane pipelines that drive both stores at once
        │
        ↓
Live store listing
```

Most teams don't work through `ios/` and `android/` as separate manual processes — `cross-platform/` describes the tooling that runs both in parallel from a single CI pipeline. But the platform-specific docs are still the reference for what that tooling is actually doing under the hood.

## 2. What's in this directory

| Directory | Covers | Start here |
|---|---|---|
| [`ios/`](ios/README.md) | App Store Connect, TestFlight, `.ipa` uploads, phased release | [build-upload.md](ios/build-upload.md), [testflight.md](ios/testflight.md) |
| [`android/`](android/README.md) | Google Play Console, `.aab` bundles, staged rollout | [app-bundle.md](android/app-bundle.md), [play-console.md](android/play-console.md) |
| [`cross-platform/`](cross-platform/README.md) | EAS Submit / Fastlane pipelines spanning both stores, shared assets and metadata | [submission.md](cross-platform/submission.md) |

## 3. Before you submit

- [ ] You know which store (or both) you're publishing to, and which pipeline you're using to get there.
- [ ] The binary uploads cleanly and shows up in the store's processing queue without errors.
- [ ] Screenshots, icons, and store text are in place and meet each store's requirements.

---

## Official sources

- Apple App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing

- `publishing/ios/README.md`
- `publishing/android/README.md`
- `publishing/cross-platform/README.md`

### Store operations

- `store-operations/README.md`
