# Role-Based Access Control (RBAC) & Least Privilege Handbook

This handbook details Role-Based Access Control (RBAC), least privilege access principles, multi-factor authentication (MFA/2FA), hardware security keys (YubiKey), and developer permissions for **Access Control** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to restrict access to code signing infrastructure.

This guide is **not**:

- an authorization mechanism to grant Account Holder / Admin roles to all team members
- a substitute for requiring mandatory 2FA on developer accounts
- a guide to sharing user accounts across multiple engineers

---

# 1. Least Privilege Access Control Matrix

Access to code signing credentials and store consoles MUST be restricted using the principle of least privilege:

```text
┌────────────────────────────────────────────────────────┐
│             LEAST PRIVILEGE ACCESS MATRIX              │
│                                                        │
│  - Account Holder / Admin: Limited to 1-2 Executives   │
│  - App Manager: Lead Release Engineers & CI Keys       │
│  - Developer: Individual Engineers (No prod signing)   │
│  - Customer Support: App Store Review responses only   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Enforced with Mandatory FIDO2 / YubiKey Hardware 2FA ]
```

---

# 2. Key Access Control Rules

1. **Mandatory 2FA**: All Apple Developer and Google Play Console accounts MUST enforce hardware-backed 2FA (Authenticator App or YubiKey).
2. **Personal Accounts Policy**: Individual developers MUST NOT use personal Apple IDs or Gmail accounts for production signing; enterprise organization accounts are required.

---

# 3. Operational Verification Checklist

- [ ] **Admin Roles Restricted**: Admin / Account Holder access limited to maximum 2 team members.
- [ ] **2FA Mandatory**: 100% of team members have 2FA enabled on Apple and Google accounts.
- [ ] **Individual Accounts Used**: Each engineer has an individual named account (zero shared credentials).

---

# 4. Official Sources

- Apple Developer Program Team Roles: https://developer.apple.com/support/roles/
- Google Play Developer Account Roles: https://support.google.com/googleplay/android-developer/answer/9844686

---

**Last verified:** August 14, 2026

---

# Related documentation

### Signing security

- `signing/security/README.md`
- `signing/security/ci-secrets.md`
- `signing/security/recovery.md`
- `signing/security/secret-storage.md`

### iOS signing

- `signing/ios/certificates.md`
- `signing/ios/key-rotation.md`

### Android signing

- `signing/android/keystore.md`
- `signing/android/key-rotation.md`

### AI security

- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`
