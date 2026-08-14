# Attribution & MMP Integrations

This guide details the integration architecture, SKAdNetwork (SKAN 4.0/5.0) framework setup, App Tracking Transparency (ATT) prompt timing, and release compliance rules for **Mobile Measurement Partners (MMP)** (AppsFlyer, Branch.io, Adjust, Singular) in React Native and Expo applications.

It specifies how to track campaign attribution and deferred deep linking while ensuring compliance with Apple App Store Review Guidelines (Guideline 5.1.2) and Google Play Advertising ID policies.

This guide is **not**:

- a marketing campaign builder
- an authorization mechanism to access IDFA without explicit user consent
- a substitute for declaring SKAdNetwork IDs in `Info.plist`

---

# 1. Integration Architecture & SKAdNetwork Setup

Mobile Measurement Partners (MMPs) measure ad campaign installs and user acquisition channels across ad networks (Meta, Google Ads, TikTok, Apple Search Ads).

```text
[ User Clicks Campaign Ad ]
            │
            ▼
[ Install App from App Store / Play Store ]
            │
            ▼
┌────────────────────────────────────────────────────────┐
│               APP LAUNCH & ATT STATUS CHECK            │
│                                                        │
│  - iOS: Check SKAdNetwork Postback / ATT Status        │
│  - Android: Check Google Advertising ID (GAID)         │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            DEFERRED DEEP LINKING RESOLUTION            │
│  - Resolve attribution payload → Route to target screen│
└────────────────────────────────────────────────────────┘
```

---

# 2. SKAdNetwork (SKAN 4.0 / 5.0) `Info.plist` Declarations

To receive conversion postbacks from ad networks without user tracking consent, ad network SKAdNetwork IDs must be declared in `ios/PrivacyInfo.xcprivacy` and `Info.plist` (or via Expo Config Plugins).

### Expo `app.json` SKAdNetwork Plugin Config

```json
{
  "expo": {
    "plugins": [
      [
        "react-native-appsflyer",
        {
          "shouldRequestLocationPermission": false
        }
      ]
    ],
    "ios": {
      "infoPlist": {
        "SKAdNetworkItems": [
          { "SKAdNetworkIdentifier": "v9wttpbfk9.skadnetwork" },
          { "SKAdNetworkIdentifier": "n38lu8d865.skadnetwork" },
          { "SKAdNetworkIdentifier": "cstr6suwn9.skadnetwork" }
        ]
      }
    }
  }
}
```

---

# 3. App Tracking Transparency (ATT) Prompt Timing

1. **Strategic Prompt Delay**: Never trigger the ATT permission prompt instantly on app launch. Allow the user to complete initial onboarding or view app value before prompting.
2. **Handle Non-Tracking State**: When ATT status is `denied` or `restricted`, the MMP SDK automatically falls back to SKAdNetwork postbacks and privacy-preserving aggregate measurement.

---

# 4. Google Play Advertising ID (GAID) Policy

For Android builds targeting Android 13+ (API Level 33+), if the application uses the Advertising ID, declare the permission in `AndroidManifest.xml`:

```xml
<uses-permission android:name="com.google.android.gms.permission.AD_ID" />
```

If the app targets children or is listed in the Google Play Families program, the Advertising ID MUST NOT be collected.

---

# 5. Operational Verification Checklist

- [ ] **SKAdNetwork IDs Declared**: Required ad network SKAN IDs included in `Info.plist`.
- [ ] **ATT Prompt Timed**: App Tracking Transparency prompt is delayed until after initial user onboarding.
- [ ] **Android GAID Permission Included**: `AD_ID` permission declared in `AndroidManifest.xml` for Android.
- [ ] **Privacy Manifest Updated**: Data collection purposes for attribution added to `PrivacyInfo.xcprivacy`.
- [ ] **Deferred Deep Links Tested**: Test installs from campaign links resolve correctly to deep-linked screens.

---

# 6. Related Documentation

- [Analytics Integration Guide](../analytics/README.md) - Analytics SDKs.
- [Privacy Auditor Agent](../../ai/agents/privacy-auditor.md) - Privacy Auditor agent definition.
- [Store Metadata Workflow](../../ai/workflows/metadata-generation.md) - Metadata compliance.

---

# 7. Official Sources

- Apple SKAdNetwork Documentation: https://developer.apple.com/documentation/storekit/skadnetwork
- Google Play Advertising ID Policy: https://support.google.com/googleplay/android-developer/answer/6034221

---

**Last verified:** August 13, 2026
