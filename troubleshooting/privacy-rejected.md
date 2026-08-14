# Privacy Manifest & Data Safety Rejection Triage

This document details rejection triage, Apple Guideline 5.1.1, missing `PrivacyInfo.xcprivacy` Required Reason APIs, and Google Play Data Safety declaration mismatches for **Privacy Rejections** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for resolving privacy-related rejection notices.

This guide is **not**:

- an authorization mechanism to omit third-party SDK data disclosures
- a substitute for inspecting Xcode Privacy Reports
- a guide to misrepresenting data collection practices

---

# 1. Privacy Rejection Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             PRIVACY REJECTION DIAGNOSTIC               │
│                                                        │
│  - Inspect Xcode Privacy Report vs App Privacy Labels  │
│  - Audit `AndroidManifest.xml` permissions vs Data Safety│
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ `PrivacyInfo.xcprivacy` Missing ] [ Play Data Safety Mismatch ]
Add `NSPrivacyCollectedDataTypes`   Update Data Safety form in Play
& Required Reason API keys          Console to match requested permissions
```

---

# 2. Key Privacy Rejection Scenarios & Resolutions

### Scenario A: Apple Guideline 5.1.1 Mismatch (Data Collection Not Declared)
- **Root Cause**: Xcode Privacy Report detected crash logging SDK (Sentry), but App Store Connect Privacy Labels declared "No Data Collected".
- **Resolution**:
  1. Generate Xcode Privacy Report.
  2. Update App Privacy Nutrition Labels in App Store Connect to declare crash diagnostics.

### Scenario B: Missing Required Reason API Declaration
- **Root Cause**: Binary uses `NSUserDefaults` without declaring a valid reason code in `PrivacyInfo.xcprivacy`.
- **Resolution**: Add `NSPrivacyAccessedAPICategoryUserDefaults` to `PrivacyInfo.xcprivacy` manifest.

---

# 3. Operational Verification Checklist

- [ ] **Xcode Privacy Report Inspected**: Privacy report generated and inspected for all bundled SDKs.
- [ ] **App Privacy Labels Aligned**: App Store Connect labels match Xcode Privacy Report 100%.
- [ ] **Required Reason APIs Declared**: User Defaults, Disk Stat APIs declared in manifest.

---

# 4. Official Sources

- Apple Describing Use of Required Reason API: https://developer.apple.com/documentation/bundleresources/describing_use_of_required_reason_api

---

**Last verified:** August 14, 2026
