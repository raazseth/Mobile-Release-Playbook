# iOS Provisioning Profiles & Entitlement Capabilities Handbook

This handbook details iOS Provisioning Profiles (App Store, Ad Hoc, Development), explicit vs wildcard App IDs, entitlement capabilities (Push, HealthKit, Associated Domains, IAP), and profile binding rules for **Provisioning Profiles** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to configure mobile provisioning profiles.

This guide is **not**:

- an authorization mechanism to use wildcard App IDs (`*`) for production releases
- a substitute for declaring native entitlement capabilities in Xcode / `app.json`
- a guide to ignoring profile expiration notices

---

# 1. Provisioning Profile Architecture

A Mobile Provisioning Profile (`.mobileprovision`) binds an Apple Distribution Certificate, an Explicit App ID, and target entitlements into a signed container installed alongside compiled iOS binaries.

```text
┌────────────────────────────────────────────────────────┐
│             PROVISIONING PROFILE CONTAINER             │
│                                                        │
│  - Apple Distribution Certificate (.p12)              │
│  - Explicit App ID (`com.example.mobileapp`)          │
│  - Entitlements (Push Notifications, Associated Domains)│
│  - Allowed Device UDIDs (Development / Ad Hoc only)    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Mobile Provisioning Profile Signed by Apple Developer Portal ]
```

---

# 2. Key Provisioning Profile Types

1. **App Store Provisioning Profile**: Used for production builds submitted to App Store Connect and TestFlight. Does NOT contain individual device UDIDs.
2. **Ad Hoc Provisioning Profile**: Used for enterprise ad-hoc distribution to up to 100 registered device UDIDs.
3. **Development Provisioning Profile**: Used for local debug builds on registered test hardware.

---

# 3. Operational Verification Checklist

- [ ] **Explicit App ID Used**: App ID matches `bundleIdentifier` exactly (`com.example.app`); wildcard IDs avoided.
- [ ] **Entitlements Matched**: Profile includes all capabilities declared in code (Push, HealthKit, Universal Links).
- [ ] **Profile Active**: Profile verified active in Apple Developer Portal.

---

# 4. Official Sources

- Apple Provisioning Profiles Guidance: https://developer.apple.com/help/account/manage-profiles/

---

**Last verified:** August 14, 2026
