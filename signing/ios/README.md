# iOS Code Signing & Provisioning Infrastructure Handbook

This directory contains production handbook guides, Apple Developer certificate workflows, provisioning profile configurations, Fastlane Match certificate synchronization, and App Store Connect `.p8` API key management for **iOS Code Signing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to manage iOS code signing credentials for manual Xcode and automated Fastlane / EAS Build CI/CD pipelines.

This guide is **not**:

- an authorization mechanism to manually generate ad-hoc certificates on individual developer laptops
- a substitute for using Fastlane Match
- a guide to sharing raw Apple Developer Account credentials

---

# 1. iOS Code Signing Architecture

iOS code signing requires three interlocking components: an **Apple Distribution Certificate**, an **Explicit App ID**, and a **Mobile Provisioning Profile**.

```text
┌────────────────────────────────────────────────────────┐
│             iOS CODE SIGNING INFRASTRUCTURE            │
│                                                        │
│  1. Apple Distribution Certificate (.p12)              │
│     (Authenticates team digital identity)              │
│                                                        │
│  2. Explicit App ID (`com.example.mobileapp`)          │
│     (Defines app capabilities & entitlements)          │
│                                                        │
│  3. Mobile Provisioning Profile (.mobileprovision)     │
│     (Binds Certificate + App ID + Entitlements)        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Encrypted Transporter Upload via App Store Connect API Key (.p8) ]
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core iOS Signing Scope | Key Components & Commands |
|---|---|---|
| **[README.md](README.md)** | Subsystem index and iOS code signing architecture. | High-level infrastructure overview. |
| **[certificates.md](certificates.md)** | Apple Distribution & Development Certificates, WWDC G3/G4 Root CAs. | Certificate generation, `.p12` export, 365-day expiry. |
| **[provisioning-profiles.md](provisioning-profiles.md)** | Provisioning Profiles (App Store, Ad Hoc, Development), entitlements. | Entitlement capabilities, Push, HealthKit, IAP. |
| **[distribution.md](distribution.md)** | Fastlane Match automated certificate sync, encrypted git backends. | `fastlane match appstore`, `fastlane match nuke`. |
| **[api-keys.md](api-keys.md)** | App Store Connect `.p8` API keys, Transporter CLI authentication. | Key ID, Issuer ID, App Manager role permissions. |
| **[key-rotation.md](key-rotation.md)** | iOS certificate and provisioning profile 90-day rotation protocol. | Zero-downtime certificate renewal procedure. |

---

# 3. Operational Verification Checklist

- [ ] **Fastlane Match Active**: `fastlane match appstore` verifies certificates match encrypted git repo.
- [ ] **Certificate Expiry Audited**: Distribution certificate has > 90 days of validity remaining.
- [ ] **`.p8` Key Validated**: App Store Connect API key authenticated cleanly with Apple servers.

---

# 4. Official Sources

- Apple Code Signing Guidance: https://developer.apple.com/support/code-signing/
- Fastlane Match Documentation: https://docs.fastlane.tools/actions/match/

---

**Last verified:** August 14, 2026

---

# Related documentation

### iOS signing

- `signing/ios/api-keys.md`
- `signing/ios/certificates.md`
- `signing/ios/distribution.md`
- `signing/ios/key-rotation.md`
- `signing/ios/provisioning-profiles.md`

### Android signing

- `signing/android/README.md`

### Signing security

- `signing/security/secret-storage.md`
- `signing/security/recovery.md`

### Store accounts

- `store-accounts/apple-developer.md`

### Troubleshooting

- `troubleshooting/signing-fails.md`
