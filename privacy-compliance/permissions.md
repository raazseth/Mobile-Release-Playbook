# Runtime Permission Requests & Disclosures Handbook

This handbook details runtime permission prompts, Android 14+ Selected Photos permission (`READ_MEDIA_VISUAL_USER_SELECTED`), Just-In-Time disclosures, and permission fallback handling for **Permissions** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to request device permissions legally and gracefully.

This guide is **not**:

- an authorization mechanism to request all permissions on app launch
- a substitute for providing clear `Info.plist` usage description strings
- a guide to crashing when permissions are denied

---

# 1. Runtime Permission Architecture & Just-In-Time Prompts

Permissions MUST be requested **Just-In-Time**—when the user actively triggers a feature requiring that permission—rather than in a batch prompt on app cold start.

```text
┌────────────────────────────────────────────────────────┐
│             JUST-IN-TIME PERMISSION WORKFLOW           │
│                                                        │
│  1. User Taps 'Upload Profile Photo' Button           │
│  2. Display In-App Contextual Pre-Prompt Explanation   │
│  3. Trigger Native OS Permission Dialog                │
│  4. Handle 'Granted', 'Denied', and 'Selected Photos'  │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Permission Granted ]                 [ Permission Denied ]
Open Camera / Photo Picker             Display graceful fallback notice with
                                       link to System Settings
```

---

# 2. Android 14+ Selected Photos Permission (`READ_MEDIA_VISUAL_USER_SELECTED`)

Android 14+ (API 34+) introduces granular photo access, allowing users to grant access to **selected photos** only rather than their entire media library:

```groovy
// android/app/src/main/AndroidManifest.xml
<uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
<uses-permission android:name="android.permission.READ_MEDIA_VIDEO" />
<uses-permission android:name="android.permission.READ_MEDIA_VISUAL_USER_SELECTED" />
```

---

# 3. Operational Verification Checklist

- [ ] **Just-In-Time Prompts Implemented**: Permissions requested only when user triggers associated feature.
- [ ] **Usage Strings Configured**: `Info.plist` includes descriptive strings (`NSCameraUsageDescription`, `NSLocationWhenInUseUsageDescription`).
- [ ] **Android 14+ Selected Photos Handled**: App supports partial photo access grant on Android 14+.
- [ ] **Denied State Handled**: App displays graceful fallback UI with settings link when permission is denied.

---

# 4. Official Sources

- Android Permissions Overview: https://developer.android.com/guide/topics/permissions/overview
- Apple Requesting Authorization: https://developer.apple.com/documentation/uikit/protecting_user_privacy/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Privacy compliance

- `privacy-compliance/README.md`
- `privacy-compliance/advertising.md`
- `privacy-compliance/analytics.md`
- `privacy-compliance/children.md`
- `privacy-compliance/data-collection.md`
- `privacy-compliance/data-deletion.md`
- `privacy-compliance/data-retention.md`
- `privacy-compliance/health-data.md`
- `privacy-compliance/privacy-policy.md`
- `privacy-compliance/regional-requirements.md`
- `privacy-compliance/tracking.md`

### Store operations

- `store-operations/data-safety.md`
- `store-operations/privacy-information.md`

### Pre-release

- `pre-release/privacy-audit.md`

### AI agents

- `ai/agents/privacy-auditor.md`

### Monetization

- `monetization/receipt-validation.md`

### Troubleshooting

- `troubleshooting/privacy-rejected.md`
