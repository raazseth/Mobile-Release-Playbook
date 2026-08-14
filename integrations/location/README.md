# Location Services Integration

This guide details the integration architecture, permission requirements, background location policies, Google Play location review declarations, and release verification rules for **Location & Geofencing Services** in React Native and Expo applications.

It specifies how to request foreground and background location access while ensuring strict compliance with Apple App Store Review Guidelines (Guideline 5.1.1, Guideline 5.1.5) and Google Play Background Location Policies.

This guide is **not**:

- an authorization mechanism to access location data without user consent
- a generic map rendering guide (see [maps/](../maps/README.md))
- a substitute for submitting required video proof for Google Play background location approvals

---

# 1. Integration Architecture & Permission Tiers

Location hardware is divided into two distinct permission tiers across iOS and Android:

```text
┌────────────────────────────────────────────────────────┐
│              FOREGROUND LOCATION PERMISSION            │
│  Access location ONLY while app is visible on screen.  │
│  iOS: `NSLocationWhenInUseUsageDescription`            │
│  Android: `ACCESS_FINE_LOCATION` / `COARSE_LOCATION`   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼ (Requires Separate User Authorization)
┌────────────────────────────────────────────────────────┐
│              BACKGROUND LOCATION PERMISSION            │
│  Access location while app is backgrounded / closed.   │
│  iOS: `NSLocationAlwaysAndWhenInUseUsageDescription`   │
│  Android: `ACCESS_BACKGROUND_LOCATION`                 │
└────────────────────────────────────────────────────────┘
```

---

# 2. iOS Usage Description Strings (`Info.plist`)

When integrating location services on iOS, clear usage description strings must be declared in `Info.plist` (via Expo `app.json` config plugins):

```json
{
  "expo": {
    "plugins": [
      [
        "expo-location",
        {
          "locationWhenInUsePermission": "FitnessTracker uses your location to track your running route while the app is active.",
          "locationAlwaysAndWhenInUsePermission": "FitnessTracker uses your background location to send geofenced entry alerts when arriving at your gym.",
          "isIosBackgroundLocationEnabled": true
        }
      ]
    ]
  }
}
```

> **WARNING (Apple Guideline 5.1.5)**: Declaring `NSLocationAlwaysAndWhenInUseUsageDescription` without a clear, essential background feature (such as turn-by-turn navigation or fitness tracking) will trigger an immediate App Store rejection.

---

# 3. Google Play Background Location Declaration & Review

Google Play enforces a strict approval process for any Android app requesting `ACCESS_BACKGROUND_LOCATION`:

1. **Demonstrate Core Necessity**: Background location MUST be essential to the app's core purpose.
2. **Submit Video Proof**: Developers must record and submit a video demonstration showing the background location feature in action during Google Play Console release declaration.
3. **Prominent In-App Disclosure**: Before triggering the runtime permission dialog, the app MUST display an in-app prominent disclosure explaining what data is collected and how it is used.

---

# 4. In-App Prominent Disclosure Specification

Before invoking the native Android location permission prompt, display a modal disclosure:

```text
┌────────────────────────────────────────────────────────┐
│              PROMINENT LOCATION DISCLOSURE             │
│                                                        │
│  FitnessTracker collects location data to enable route │
│  tracking even when the app is closed or not in use.   │
│                                                        │
│  [ Agree & Continue ]          [ No Thanks ]           │
└────────────────────────────────────────────────────────┘
```

---

# 5. Operational Verification Checklist

- [ ] **Foreground Usage Strings Clear**: `NSLocationWhenInUseUsageDescription` clearly explains feature value.
- [ ] **Background Strings Justified**: `NSLocationAlwaysAndWhenInUseUsageDescription` included ONLY if essential.
- [ ] **In-App Disclosure Displayed**: Prominent disclosure modal displayed before Android background location request.
- [ ] **Google Play Video Submitted**: Video proof of background location recorded for Google Play Console declaration.
- [ ] **Fallback Active**: App operates in degraded mode if location permission is denied.

---

# 6. Related Documentation

- [Maps Integration Guide](../maps/README.md) - Map rendering and markers.
- [Privacy Auditor Agent](../../ai/agents/privacy-auditor.md) - Privacy Auditor agent definition.
- [Release Preparation Workflow](../../ai/workflows/release-preparation.md) - Release preparation.

---

# 7. Official Sources

- Apple Location & Privacy Guidelines: https://developer.apple.com/documentation/corelocation/requesting_authorization_for_location_services
- Google Play Background Location Policy: https://support.google.com/googleplay/android-developer/answer/9799150

---

**Last verified:** August 13, 2026
