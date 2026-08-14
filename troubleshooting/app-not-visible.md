# App Store Visibility & Search Indexing Triage

This document details diagnostic workflows, App Store search indexing propagation delays, Google Play Console country availability restrictions, and Target API Level search filtering for **App Visibility Troubleshooting** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for diagnosing why a published app is not visible on mobile store viewports.

This guide is **not**:

- an authorization mechanism to manipulate store search rankings artificially
- a substitute for verifying store country availability settings
- a guide to keyword stuffing

---

# 1. App Visibility Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             APP VISIBILITY DIAGNOSTIC FLOW             │
│                                                        │
│  - App status marked 'Ready for Sale' / 'Published'    │
│  - App does not appear when searching exact name       │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Index Propagation Delay (24-48h) ] [ Target API / OS Filtering ]
App Store & Play search indexing   Old Android devices running lower
requires 24-48 hours post-approval OS hidden by Target API 36 rules
```

---

# 2. Key Visibility Issues & Resolutions

### Scenario A: App Approved but Not Searchable (Index Delay)
- **Root Cause**: Store search engines take up to 24-48 hours to build search index tables post-approval.
- **Resolution**: Direct users via direct store links (`https://apps.apple.com/app/id...` or `https://play.google.com/store/apps/details?id=...`) while search indexing completes.

### Scenario B: Android App Not Searchable on Certain Devices
- **Root Cause**: Device OS is below app's `minSdkVersion` or device hardware lacks required feature declared in `AndroidManifest.xml` (e.g., `<uses-feature android:name="android.hardware.camera" android:required="true" />`).
- **Resolution**: Set `android:required="false"` for optional hardware features in `AndroidManifest.xml` to avoid filtering out devices.

---

# 3. Operational Verification Checklist

- [ ] **Direct Store Links Verified**: Direct URL links open store product page correctly on live devices.
- [ ] **Country Availability Checked**: Target countries selected in Play Console and App Store Connect.
- [ ] **`minSdkVersion` Audited**: Minimum SDK set appropriately to maximize device reach.

---

# 4. Official Sources

- Apple App Store Search Indexing: https://developer.apple.com/help/app-store-connect/#/dev02c0507a2
- Google Play Device Filtering: https://support.google.com/googleplay/android-developer/answer/9859751

---

**Last verified:** August 14, 2026
