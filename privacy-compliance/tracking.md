# App Tracking Transparency (ATT) Framework & IDFA Handbook

This handbook details the iOS App Tracking Transparency (ATT) framework, Advertising Identifier (IDFA) timing rules, SKAdNetwork 4.0/5.0 integration, and Google Advertising ID (`AD_ID`) declarations for **App Tracking** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to request tracking authorization legally without triggering instant App Review rejections.

This guide is **not**:

- an authorization mechanism to access the IDFA before displaying the native ATT prompt
- a substitute for configuring SKAdNetwork ad attribution
- a guide to fingerprinting devices (fingerprinting is strictly forbidden by Apple)

---

# 1. App Tracking Transparency (ATT) Architecture

Apple strictly mandates that access to the device Advertising Identifier (IDFA) requires explicit user consent via the native App Tracking Transparency prompt.

```text
┌────────────────────────────────────────────────────────┐
│             APP TRACKING TRANSPARENCY (ATT) FLOW       │
│                                                        │
│  1. Check ATT Tracking Status via `TrackingTransparency`│
│  2. If `notDetermined`: Prompt Native ATT Dialog       │
│  3. If `authorized`: Initialize MMP SDK & IDFA Access │
│  4. If `denied`: Set MMP SDK to Non-Tracking Mode      │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ `NSUserTrackingUsageDescription` Mandated in Info.plist ]
```

---

# 2. React Native ATT Implementation Example (`expo-tracking-transparency`)

```typescript
import { requestTrackingPermissionsAsync, getTrackingPermissionsAsync } from 'expo-tracking-transparency';
import { useEffect } from 'react';

export function useATTAuthorization() {
  useEffect(() => {
    async function checkATT() {
      const { status } = await getTrackingPermissionsAsync();
      if (status === 'undetermined') {
        // Request ATT permission from user
        const { status: newStatus } = await requestTrackingPermissionsAsync();
        if (newStatus === 'granted') {
          // Initialize MMP Ad Attribution SDK (AppsFlyer / Adjust)
          initializeTrackingSDK();
        }
      }
    }
    checkATT();
  }, []);
}
```

---

# 3. Operational Verification Checklist

- [ ] **ATT String Configured**: `NSUserTrackingUsageDescription` string included in `Info.plist`.
- [ ] **No IDFA Access Before Prompt**: MMP SDK does not access IDFA prior to ATT user authorization.
- [ ] **SKAdNetwork Configured**: SKAdNetwork IDs declared in `Info.plist` for ad attribution.
- [ ] **`AD_ID` Declared on Android**: `com.google.android.gms.permission.AD_ID` declared in `AndroidManifest.xml`.

---

# 4. Official Sources

- Apple App Tracking Transparency: https://developer.apple.com/documentation/apptrackingtransparency
- SKAdNetwork Documentation: https://developer.apple.com/documentation/storekit/skadnetwork

---

**Last verified:** August 14, 2026
