# iOS Certificate & Provisioning Profile Rotation Protocol

This handbook details the 90-day pre-expiration audit cycle, zero-downtime certificate renewals, revoking compromised certificates, and profile regeneration workflows for **iOS Key Rotation** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to rotate iOS signing credentials safely.

This guide is **not**:

- an authorization mechanism to delay certificate rotation until after expiration
- a substitute for executing zero-downtime certificate renewal protocols
- a guide to revoking active distribution certificates without checking live TestFlight builds

---

# 1. Zero-Downtime Certificate Rotation Workflow

Apple allows up to **2 active Apple Distribution Certificates** concurrently per Developer Account, enabling seamless zero-downtime rotation.

```text
┌────────────────────────────────────────────────────────┐
│             ZERO-DOWNTIME ROTATION WORKFLOW            │
│                                                        │
│  1. Audit: Expiration alert fires at 90 days remaining.│
│  2. Generate: Create 2nd Apple Distribution Cert.     │
│  3. Re-Match: Run `fastlane match appstore --force`.   │
│  4. Deploy: Update CI secrets with new certificate.   │
│  5. Revoke: Revoke old certificate after 30 days.      │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Rotation Verification Rules

- **90-Day Warning Window**: CI audit scripts raise warnings when distribution certificates have ≤ 90 days of validity remaining.
- **TestFlight Compatibility**: Existing live TestFlight builds signed with the old certificate remain valid until the certificate expires.

---

# 3. Operational Verification Checklist

- [ ] **Expiration Audited**: Certificates audited and expiration dates logged in release calendar.
- [ ] **Dual Certificate Strategy**: Second distribution certificate generated prior to revoking primary.
- [ ] **Fastlane Match Updated**: Match repository synced with new certificate and provisioning profiles.

---

# 4. Official Sources

- Apple Certificate Expiration Guidance: https://developer.apple.com/support/certificates/

---

**Last verified:** August 14, 2026

---

# Related documentation

### iOS signing

- `signing/ios/README.md`
- `signing/ios/api-keys.md`
- `signing/ios/certificates.md`
- `signing/ios/distribution.md`
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
