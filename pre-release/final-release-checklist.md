# Final Release Verification Checklist

This handbook details the final pre-flight verification checklist executed prior to submitting release candidate binaries to **App Store Connect** and **Google Play Console**.

Engineered in alignment with **2026 platform specifications**, it provides an exhaustive technical pre-flight inspection gate.

This guide is **not**:

- an authorization mechanism to submit un-verified builds
- a substitute for executing pre-release audits
- a guide to skipping store asset verification

---

# 1. Pre-Flight Verification Architecture

The Final Release Verification Checklist serves as the last operational barrier before triggering store review submissions.

```text
┌────────────────────────────────────────────────────────┐
│             PRE-FLIGHT VERIFICATION CHECKLIST          │
│                                                        │
│  [1. Binary Build Compilation & Signatures]            │
│  [2. Store Listing Metadata & Localized Assets]        │
│  [3. Demo Account & Reviewer Notes Preparation]        │
│  [4. Privacy Manifest & Data Safety Alignment]         │
│  [5. Staged Rollout Schedule & Emergency Halt Ready]   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ 100% CHECKLIST PASS ──→ Submit Release Candidate to Store Review ]
```

---

# 2. Final Pre-Flight Checkpoints

### Section 1: Binary Build & Code Signing
- [ ] **iOS `.ipa` Archive**: Signed with active Apple Distribution Certificate & Production Provisioning Profile.
- [ ] **Android `.aab` Bundle**: Compiled targeting Android 16 (API Level 36) with R8 minification (`minifyEnabled true`).
- [ ] **Debug Symbols Exported**: `.dSYM` symbols (iOS) and `mapping.txt` (Android) uploaded to Sentry.
- [ ] **Export Compliance Set**: `ITSAppUsesNonExemptEncryption: false` declared in `Info.plist`.

### Section 2: Store Metadata & Assets
- [ ] **App Title Length**: Title ≤ 30 characters on both platforms; zero competitor trademarks (Guideline 2.3.7).
- [ ] **Subtitle / Short Description**: Subtitle ≤ 30c (iOS); Short Description ≤ 80c (Android).
- [ ] **Keywords Formatted**: iOS keywords ≤ 100c, comma-separated without spaces.
- [ ] **Screenshots Validated**: 1320x2868 px portrait set uploaded for 6.9" display class; alpha transparency stripped (`hasAlpha: NO`).
- [ ] **Feature Graphic Uploaded**: 1024x500 px Feature Graphic uploaded for Google Play.

### Section 3: Privacy & Review Readiness
- [ ] **Privacy Manifests Included**: `PrivacyInfo.xcprivacy` verified for all bundled iOS SDKs.
- [ ] **Data Safety Aligned**: Data Safety declarations match requested permissions and privacy policy.
- [ ] **Demo Account Active**: Demo credentials (`[username] / [password]`) verified working on production backend with 2FA bypass.
- [ ] **Web Deletion URL Live**: Public web URL (`https://example.com/delete-account`) verified live.

---

# 3. Operational Verification Checklist

- [ ] **Pre-Flight Complete**: All 3 sections verified and signed off by Lead Release Engineer.
- [ ] **Binary Locked**: SHA-256 binary hash verified identical to tested release candidate.
- [ ] **Rollout Plan Active**: Phased release schedule (iOS 7-day) and staged rollout (Android 1%) configured.

---

# 4. Official Sources

- Apple Submit for Review Guide: https://developer.apple.com/help/app-store-connect/#/dev8b49e0c52
- Google Play Release Guide: https://support.google.com/googleplay/android-developer/answer/9859751

---

**Last verified:** August 14, 2026
