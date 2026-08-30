# Apple App Privacy Labels & PrivacyInfo Manifest Alignment Handbook

This handbook details the configuration requirements, data collection categories, Required Reason API declarations, and `PrivacyInfo.xcprivacy` manifest alignment for **Apple App Privacy Labels** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive technical and operational guidance for completing Apple's Privacy Nutrition Labels in App Store Connect while ensuring 100% agreement with compiled binary privacy manifests.

This guide is **not**:

- an authorization mechanism to omit third-party SDK privacy declarations
- an optional form (App Privacy Labels are mandatory for all iOS submissions)
- a substitute for generating Xcode Privacy Reports

---

# 1. App Privacy Label Taxonomy & Classification

Apple divides privacy disclosures into three primary data collection classifications in App Store Connect:

```text
                                APPLE APP PRIVACY LABELS
                                           │
       ┌───────────────────────────────────┼───────────────────────────────────┐
       ▼                                   ▼                                   ▼
[ Data Used to Track You ]       [ Data Linked to You ]              [ Data Not Linked to You ]
Data tied to third-party         Data linked to user identity        Anonymous usage telemetry
tracking/IDFA across apps        (User ID, Email, Purchases)         (Crash diagnostics, Performance)
```

| Privacy Classification | Description | Example Data Types | Key Store Rule |
|---|---|---|---|
| **Data Used to Track You** | Data linked with third-party data for targeted advertising or measurement | Device ID (IDFA), Ad Data | Requires ATT Permission Prompt |
| **Data Linked to You** | Data collected and associated with user's account identity | Email, Name, Purchase History | Must declare in App Privacy Questionnaire |
| **Data Not Linked to You** | Anonymous data collected without user identity link | Anonymous Crash Logs, Performance | Must declare in App Privacy Questionnaire |

---

# 2. `PrivacyInfo.xcprivacy` Manifest Alignment (Guideline 5.1.1)

Apple merges privacy manifests from your app binary and all compiled third-party SDKs into a unified Xcode Privacy Report.

```xml
<!-- Example: PrivacyInfo.xcprivacy File -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>NSPrivacyTracking</key>
    <false/>
    <key>NSPrivacyCollectedDataTypes</key>
    <array>
        <dict>
            <key>NSPrivacyCollectedDataType</key>
            <string>NSPrivacyCollectedDataTypeCrashData</string>
            <key>NSPrivacyCollectedDataTypeLinked</key>
            <false/>
            <key>NSPrivacyCollectedDataTypeTracking</key>
            <false/>
            <key>NSPrivacyCollectedDataTypePurposes</key>
            <array>
                <string>NSPrivacyCollectedDataTypePurposeAnalytics</string>
            </array>
        </dict>
    </array>
</dict>
</plist>
```

> **REJECTION WARNING**: If your App Privacy Labels in App Store Connect claim "No Data Collected", but an embedded SDK (e.g., Sentry, Firebase) includes a `PrivacyInfo.xcprivacy` manifest declaring crash data collection, Apple will reject the submission for privacy mismatch.

---

# 3. Operational Verification Checklist

- [ ] **Privacy Report Generated**: Xcode Privacy Report inspected to verify all embedded SDK privacy manifests.
- [ ] **App Privacy Labels Aligned**: App Store Connect labels match Xcode Privacy Report 100%.
- [ ] **Required Reason APIs Declared**: Disk stat, User Defaults, boot time APIs declared in `PrivacyInfo.xcprivacy`.
- [ ] **Tracking Status Accurate**: `NSPrivacyTracking` set to `true` ONLY if IDFA tracking is active.
- [ ] **Privacy Policy URL Live**: Privacy Policy web page link active and up to date.

---

# 4. Related Documentation

- [Data Safety Handbook](data-safety.md) - Google Play Data Safety.
- [App Listing Handbook](app-listing.md) - Listing setup.
- [Privacy Auditor Agent](../ai/agents/privacy-auditor.md) - Privacy auditing.

---

# 5. Official Sources

- Apple App Privacy Details Help: https://developer.apple.com/app-store/app-privacy-details/
- Apple Describing Use of Required Reason API: https://developer.apple.com/documentation/bundleresources/describing_use_of_required_reason_api

---

**Last verified:** August 14, 2026

