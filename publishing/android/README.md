# Android Publishing Handbook & Track Administration

This directory covers build compilation, release track workflows, staged rollout, and store review compliance for **Android App Publishing** in Expo and React Native applications — target API level requirements, the Android App Bundle (`.aab`) format, Google Play Billing, and closed testing rules for Personal developer accounts, covering how to compile, test, stage, and publish Android applications on Google Play.

This guide is **not**:

- an authorization mechanism to upload raw APK files to the Google Play Store (`.aab` format is mandatory)
- a substitute for performing pre-launch automated testing on Google Cloud test matrices
- a guide to bypassing Google Play Developer Distribution Agreements (DDA)

---

# 1. Architecture of Android Release Pipelines

Android application publishing follows a structured multi-track progression from local `.aab` compilation through automated testing pipelines to global production staged rollouts.

```text
Android App Bundle (.aab) compiled
  - targets current required API level, R8 obfuscation + resource shrinking
  - signed with the upload key (Play App Signing active)
        │
        ↓
Google Play Console track pipeline
  1. Internal testing track    (instant access, up to 100 testers)
  2. Closed testing track      (alpha/beta, minimum tester gate for Personal accounts)
  3. Open testing track        (public beta)
  4. Production track          (staged rollout: 1% → 100%)
        │
        ↓
Pre-launch report (Firebase Test Lab device matrix scan)
  - crash monitoring, accessibility checks
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Purpose & Scope | Key Platform & Store Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, Android release architecture, and universal publishing rules. | High-level Android release pipeline, 2026 platform rules. |
| **[app-bundle.md](app-bundle.md)** | Android App Bundle (`.aab`) compilation, R8 obfuscation, and Target API Level 36. | Target API 36 (Android 16), `.aab` format, Play App Signing. |
| **[play-console.md](play-console.md)** | Google Play Console administration, service account deployment keys, and API access. | Service Account JSON keys, Fastlane Supply, EAS Submit setup. |
| **[internal-testing.md](internal-testing.md)** | Internal Testing Track setup, rapid iteration, and instant build distribution. | Up to 100 internal testers, instant availability, zero review delays. |
| **[closed-testing.md](closed-testing.md)** | Closed Testing Tracks (Alpha/Beta) and Personal account closed testing requirements. | Mandatory 12 opted-in testers for 14 continuous days for Personal accounts. |
| **[production-release.md](production-release.md)** | Staged rollouts (1% to 100%), rollout halting, emergency rollbacks, and releases. | Staged rollout percentages (1% → 5% → 10% → 20% → 50% → 100%). |
| **[app-review.md](app-review.md)** | Google Play App Review policies, automated pre-launch reports, and policy appeals. | Pre-launch Firebase Test Lab scans, policy triage, appeal forms. |
| **[metadata.md](metadata.md)** | Google Play Store listing text metadata (Title 30c, Short Desc 80c, Long Desc 4000c). | Title 30c, Short Desc 80c, keyword density 2-3%, policy compliance. |
| **[screenshots.md](screenshots.md)** | Android phone, 7-inch tablet, 10-inch tablet, and Feature Graphic (1024x500) specs. | Min 2 max 8 phone screenshots, 1024x500 feature graphic, tablet sets. |

---

# 3. Universal Android Publishing Rules

All Android publishing implementations in this playbook must adhere to five mandatory engineering rules:

- [ ] **Target API level current**: new app bundles target Android 16 (API level 36) or higher — Google Play enforces this from August 31, 2026, with extensions available to November 1, 2026 for apps that request one. Existing apps need API level 35+ to stay visible to new users. Verify the current requirement against [Google's target API level page](https://support.google.com/googleplay/android-developer/answer/11926878) before relying on this number — it moves every year.
- [ ] **`.aab` format only**: submissions are Android App Bundles, not legacy APKs — Google Play rejects raw APK uploads.
- [ ] **Play App Signing enabled**: the developer signs builds with an upload key; Google Play re-signs the delivered APKs with the app signing key it holds.
- [ ] **Staged rollout on every production release**: start at 1% or 5%, not 100% — see `production-release.md`.
- [ ] **Pre-launch report checked before expanding a rollout**: the Firebase Test Lab automated scan is reviewed for crash spikes, accessibility warnings, or rendering regressions.

---

# Related documentation

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

---

# Official sources

- Google Play Console Release Management: https://support.google.com/googleplay/android-developer/answer/9859751
- Android Target API Level Requirements: https://developer.android.com/google/play/requirements/target-sdk
- Android App Bundle Documentation: https://developer.android.com/guide/app-bundle

---

**Last verified:** August 14, 2026

