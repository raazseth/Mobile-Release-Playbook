# Apple Developer Certificates & Certificate Authority (CA) Handbook

This handbook details Apple Development and Distribution Certificates, `.p12` bundle exports, Apple WWDC G3/G4 Intermediate Certificate Authorities, and 365-day expiration management for **Apple Certificates** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to manage iOS digital signing identities.

This guide is **not**:

- an authorization mechanism to generate ad-hoc certificates on local laptops without Fastlane Match
- a substitute for installing Apple WWDC Intermediate Certificate Authorities
- a guide to storing un-encrypted `.p12` private keys in public code repositories

---

# 1. Apple Certificate Architecture & Chain of Trust

Apple code signing relies on a cryptographic Chain of Trust linking team certificates to Apple Root CAs.

```text
┌────────────────────────────────────────────────────────┐
│             APPLE CERTIFICATE CHAIN OF TRUST           │
│                                                        │
│  [ Apple Root Certificate Authority (Root CA) ]        │
│                         │                              │
│                         ▼                              │
│  [ Apple Worldwide Developer Relations Intermediate CA ]│
│    (Apple WWDC G3 / G4 Intermediate Certificate)       │
│                         │                              │
│                         ▼                              │
│  [ Apple Distribution Certificate: Team Name (ID) ]    │
│    (Valid 365 Days ──→ Signed `.ipa` Executable)      │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Certificate Types & Specifications

- **Apple Development Certificate**: Used by engineers to sign debug builds for testing on registered development hardware devices.
- **Apple Distribution Certificate**: Used to sign production release builds submitted to App Store Connect or distributed via TestFlight. Valid for **365 days** (1 year). Max **2 active distribution certificates** per Apple Developer Account.

---

# 3. Operational Verification Checklist

- [ ] **WWDC Intermediate Installed**: Apple WWDC G3/G4 Intermediate CA installed in System Keychain.
- [ ] **Distribution Cert Valid**: Apple Distribution Certificate valid and > 90 days from expiration.
- [ ] **`.p12` Password Protected**: Exported `.p12` certificates protected with strong random passphrase.

---

# 4. Official Sources

- Apple Certificate Management: https://developer.apple.com/account/resources/certificates/list

---

**Last verified:** August 14, 2026

---

# Related documentation

### iOS signing

- `signing/ios/README.md`
- `signing/ios/api-keys.md`
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
