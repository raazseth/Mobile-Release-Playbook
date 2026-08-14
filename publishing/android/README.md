# Android Publishing Handbook & Track Administration

This directory contains production handbook guides, build compilation standards, release track workflows, staged rollout protocols, and store review compliance rules for **Android App Publishing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**—specifically target **Android 16 (API Level 36)** requirements, Android App Bundle (`.aab`) mandates, Google Play Billing 8.0/9.0+ gates, and Personal Account closed testing rules—it details how to compile, test, stage, and publish Android applications on the Google Play Store.

This guide is **not**:

- an authorization mechanism to upload raw APK files to the Google Play Store (`.aab` format is mandatory)
- a substitute for performing pre-launch automated testing on Google Cloud test matrices
- a guide to bypassing Google Play Developer Distribution Agreements (DDA)

---

# 1. Architecture of Android Release Pipelines

Android application publishing follows a structured multi-track progression from local `.aab` compilation through automated testing pipelines to global production staged rollouts.

```text
┌────────────────────────────────────────────────────────┐
│           ANDROID APP BUNDLE (.aab) COMPILATION        │
│  - Target API Level 36 (Android 16)                    │
│  - R8 Code Obfuscation & Resource Shrinking           │
│  - Signed with Upload Key (Play App Signing Active)    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY CONSOLE TRACK PIPELINE         │
│                                                        │
│  1. Internal Testing Track (Instant access; 100 testers)│
│  2. Closed Testing Track (Alpha/Beta; 12-tester gate)  │
│  3. Open Testing Track (Public beta testing)           │
│  4. Production Track (Staged Rollout: 1% → 100%)       │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             PRE-LAUNCH REPORT & AUTOMATED QA           │
│  - Automated Firebase Test Lab device matrix scan      │
│  - Crash monitoring & Accessibility checks             │
└────────────────────────────────────────────────────────┘
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

```text
1. Target API Level 36 (Android 16) Compliance
   All new Android App Bundles (.aab) submitted to Google Play MUST target Android 16 (API Level 36)
   or higher to pass Google Play Console submission validation gates.

2. Android App Bundle (.aab) Mandatory Format
   App submissions MUST be compiled as Android App Bundles (.aab). Legacy APK files are strictly
   rejected for Google Play Store distribution.

3. Google Play App Signing Mandatory Integration
   Production App Bundles MUST use Google Play App Signing. Developers sign builds with an Upload Key,
   and Google Play signs the final delivered APKs with the master App Signing Key.

4. Staged Rollouts for Production Releases
   All production releases MUST be deployed using Google Play Staged Rollouts (starting at 1% or 5%).
   Never deploy a new production version directly to 100% of users without staging.

5. Pre-Launch Report Inspection Before Rollout
   Release managers MUST inspect the Google Play Pre-Launch Report (Firebase Test Lab automated scan)
   for crash spikes, accessibility warnings, or rendering regressions before expanding a rollout.
```

---

# 4. Related Repository Documentation

- [iOS Publishing Handbook](../ios/README.md) - App Store publishing.
- [Cross-Platform Publishing Handbook](../cross-platform/README.md) - EAS Build & Submit.
- [Signing Android Handbook](../../signing/android/keystore.md) - Keystores & upload keys.

---

# 5. Official Sources

- Google Play Console Release Management: https://support.google.com/googleplay/android-developer/answer/9859751
- Android Target API Level Requirements: https://developer.android.com/google/play/requirements/target-sdk
- Android App Bundle Documentation: https://developer.android.com/guide/app-bundle

---

**Last verified:** August 14, 2026
