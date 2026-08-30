# Analytics SDK Integrations

This guide details the integration architecture, privacy manifest alignment, App Tracking Transparency (ATT) compliance, and release engineering rules for **Analytics & Telemetry SDKs** (Segment, Mixpanel, Amplitude, Firebase Analytics) in React Native and Expo applications.

It provides step-by-step guidance for configuring event tracking while ensuring strict compliance with Apple App Store Review Guidelines (Guideline 5.1.1, Guideline 5.1.2) and Google Play Data Safety policies.

This guide is **not**:

- a marketing taxonomy guide for naming custom event triggers
- a recommendation to bypass Apple App Tracking Transparency (ATT) prompts
- an authorization mechanism to collect sensitive user PII (passwords, credit card numbers, health data)

---

# 1. Integration Architecture & Data Flow

Analytics SDKs ingest user interaction events and transmit telemetry to analytics cloud platforms. In mobile applications, analytics tracking must pass through a strict privacy filter before transmission.

```text
[ User Interaction Event ]
            │
            ▼
[ Event Sanitizer / PII Filter ]
(Strip Email, Passwords, Health Data, Raw Location)
            │
            ▼
┌────────────────────────────────────────────────────────┐
│            APP TRACKING TRANSPARENCY CHECK             │
│                                                        │
│  - Is ATT Authorization Status `authorized`?           │
│  - Yes → Transmit Event with IDFA / Advertising ID     │
│  - No  → Transmit Event with Anonymous Session ID Only │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            ANALYTICS PROVIDER ENDPOINT                 │
│      (Segment / Mixpanel / Amplitude / Firebase)       │
└────────────────────────────────────────────────────────┘
```

---

# 2. Privacy Manifest & Required Reason Declarations (Guideline 5.1.1)

All analytics SDKs integrated into iOS binaries must be declared in `ios/PrivacyInfo.xcprivacy` (or via Expo Config Plugins in `app.json`).

```json
{
  "expo": {
    "plugins": [
      [
        "expo-tracking-transparency",
        {
          "userTrackingPermission": "This app uses analytics to improve app performance and user experience."
        }
      ]
    ]
  }
}
```

### Apple `PrivacyInfo.xcprivacy` Schema for Analytics

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>NSPrivacyCollectedDataTypes</key>
    <array>
        <dict>
            <key>NSPrivacyCollectedDataType</key>
            <string>NSPrivacyCollectedDataTypeProductInteraction</string>
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

---

# 3. App Tracking Transparency (ATT) Implementation

If analytics events include device advertising identifiers (IDFA) or cross-app tracking data:

1. **Request Permission In-Context**: Do not prompt for ATT on initial cold launch without user context. Trigger ATT prompt after user onboarding or when accessing a relevant feature.
2. **Handle Rejection Gracefully**: If the user selects "Ask App Not to Track", disable IDFA collection in the analytics SDK initialization parameters.

```javascript
import { requestTrackingPermissionsAsync } from 'expo-tracking-transparency';

export async function initializeAnalytics() {
  const { status } = await requestTrackingPermissionsAsync();
  const isTrackingAuthorized = status === 'granted';

  // Initialize SDK with tracking flag
  AnalyticsSDK.init({
    writeKey: 'YOUR_SEGMENT_WRITE_KEY',
    collectAdvertisingId: isTrackingAuthorized,
  });
}
```

---

# 4. Google Play Data Safety Alignment

When submitting an app using analytics to Google Play, the Data Safety questionnaire must match SDK behavior:

- **Data Types Collected**: App interactions, crash logs, performance diagnostics.
- **Collection Purpose**: Analytics, App Functionality.
- **Data Ephemeral / Encrypted**: Data is encrypted in transit via HTTPS (`TLS 1.3`).

---

# 5. Operational Verification Checklist

- [ ] **No PII Transmitted**: Analytics payloads verified free of passwords, tokens, full names, or credit card data.
- [ ] **ATT Framework Handled**: App Tracking Transparency prompt implemented for iOS if IDFA is used.
- [ ] **PrivacyInfo.xcprivacy Declared**: Analytics data collection purposes included in iOS privacy manifest.
- [ ] **HTTPS Enforced**: Analytics SDK endpoints use active HTTPS protocols (`https://`).
- [ ] **Google Data Safety Aligned**: Google Play Data Safety form matches declared analytics collection.

---

# 6. Related Documentation

- [Privacy Auditor Agent](../../ai/agents/privacy-auditor.md) - Privacy Auditor agent definition.
- [Store Metadata Workflow](../../ai/workflows/metadata-generation.md) - Metadata and privacy compliance.
- [Attribution Integration Guide](../attribution/README.md) - Mobile measurement partners.

---

# 7. Official Sources

- Apple App Store Review Guidelines (Guideline 5.1.1 & 5.1.2): https://developer.apple.com/app-store/review/guidelines/#data-collection-and-storage
- Apple App Tracking Transparency Framework: https://developer.apple.com/documentation/apptrackingtransparency
- Google Play Data Safety Policy: https://support.google.com/googleplay/android-developer/answer/10787469

---

**Last verified:** August 13, 2026

