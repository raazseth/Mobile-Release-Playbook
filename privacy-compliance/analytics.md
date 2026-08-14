# Privacy-Preserving Mobile Analytics Handbook

This handbook details anonymous telemetry, IP anonymization, opt-out toggles, data minimization, and privacy controls for **Mobile Analytics** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to collect analytics telemetry without violating user privacy rights.

This guide is **not**:

- an authorization mechanism to collect personally identifiable information (PII) without consent
- a substitute for providing analytics opt-out toggles
- a guide to tracking users across un-related apps without ATT consent

---

# 1. Privacy-Preserving Analytics Architecture

Analytics frameworks (Firebase, Mixpanel, Segment) MUST be configured to anonymize telemetry data and respect user opt-out preferences.

```text
┌────────────────────────────────────────────────────────┐
│             PRIVACY-PRESERVING ANALYTICS               │
│                                                        │
│  - Anonymize IP Addresses (`anonymize_ip: true`)       │
│  - Strip PII (Names, Emails, Passwords) from Events    │
│  - Provide Analytics Opt-Out Toggle in Settings        │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Opt-In / Default ]                   [ User Opts Out ]
Send Anonymous Telemetry              Disable Analytics Telemetry
```

---

# 2. Analytics Opt-Out Toggle Implementation

```typescript
import analytics from '@react-native-firebase/analytics';

export async function setAnalyticsCollectionEnabled(enabled: boolean) {
  // Enable or disable Firebase Analytics collection dynamically
  await analytics().setAnalyticsCollectionEnabled(enabled);
}
```

---

# 3. Operational Verification Checklist

- [ ] **IP Anonymization Active**: Analytics SDK configured to anonymize user IP addresses.
- [ ] **Zero PII in Events**: Event parameters audited to ensure zero PII (email, phone, name) is transmitted.
- [ ] **Opt-Out Toggle Functional**: App Settings menu provides functional analytics opt-out toggle.

---

# 4. Official Sources

- Firebase Analytics Privacy: https://firebase.google.com/docs/analytics/configure-data-collection

---

**Last verified:** August 14, 2026
