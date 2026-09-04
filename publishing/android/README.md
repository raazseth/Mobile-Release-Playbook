# Android Publishing

This directory covers building an `.aab`, moving it through Google Play's testing tracks, getting past review, and rolling a release out gradually with a staged rollout — target API levels, the Android App Bundle format, Google Play Billing, and the closed-testing requirements Personal developer accounts have to satisfy.

This guide is **not**:

- an authorization mechanism to upload a raw APK to Google Play — the `.aab` format is mandatory
- a substitute for running pre-launch automated tests on Google's device matrix
- a guide to working around the Google Play Developer Distribution Agreement

---

## 1. The pipeline, end to end

```text
Android App Bundle (.aab) compiled
  - targets the current required API level, R8 obfuscation + resource shrinking
  - signed with the upload key (Play App Signing active)
        │
        ↓
Google Play Console track pipeline
  1. Internal testing    (instant access, up to 100 testers)
  2. Closed testing      (alpha/beta, minimum tester gate for Personal accounts)
  3. Open testing        (public beta)
  4. Production          (staged rollout: 1% → 100%)
        │
        ↓
Pre-launch report (Firebase Test Lab device matrix scan)
  - crash monitoring, accessibility checks
```

## 2. What's in this directory

| Guide | Covers |
|---|---|
| [app-bundle.md](app-bundle.md) | `.aab` compilation, R8 obfuscation, current target API level |
| [play-console.md](play-console.md) | Play Console administration, service account keys, API access |
| [internal-testing.md](internal-testing.md) | Internal Testing Track setup and instant distribution |
| [closed-testing.md](closed-testing.md) | Closed testing tracks, and the Personal-account testing gate |
| [production-release.md](production-release.md) | Staged rollout, halting a rollout, monitoring during release |
| [app-review.md](app-review.md) | Play policy review, pre-launch reports, appeals |
| [metadata.md](metadata.md) | Store listing text and character limits |
| [screenshots.md](screenshots.md) | Phone, tablet, and Feature Graphic specs |

## 3. Rules that apply to every guide in this directory

- **Keep the target API level current.** New app bundles need to target Android 16 (API level 36) or higher — Google Play enforces this from August 31, 2026, with extensions available to November 1, 2026 for apps that request one. Existing apps need API level 35+ to stay visible to new users. Check [Google's target API level page](https://support.google.com/googleplay/android-developer/answer/11926878) before relying on this number — it moves every year.
- **Submit `.aab`, not `.apk`.** Google Play rejects raw APK uploads.
- **Turn on Play App Signing.** You sign builds with an upload key; Google re-signs the delivered APKs with the app signing key it holds.
- **Stage every production release.** Start at 1% or 5%, not 100% — see [production-release.md](production-release.md).
- **Check the pre-launch report before expanding a rollout.** It's Firebase Test Lab's automated scan for crash spikes, accessibility warnings, and rendering regressions.

---

## Official sources

- Google Play Console release management: https://support.google.com/googleplay/android-developer/answer/9859751
- Android target API level requirements: https://developer.android.com/google/play/requirements/target-sdk
- Android App Bundle documentation: https://developer.android.com/guide/app-bundle

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (Android)

- `publishing/android/app-bundle.md`
- `publishing/android/app-review.md`
- `publishing/android/closed-testing.md`
- `publishing/android/internal-testing.md`
- `publishing/android/metadata.md`
- `publishing/android/play-console.md`
- `publishing/android/production-release.md`
- `publishing/android/screenshots.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Android signing

- `signing/android/README.md`
- `signing/android/play-app-signing.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
