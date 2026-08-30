# App Store Connect API Keys (`.p8`) Handbook

This handbook details App Store Connect API key (`.p8`) generation, Key ID, Issuer ID, App Manager role permissions, Transporter CLI authentication, and secret storage for **App Store Connect API Keys** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to authenticate automated upload tools without using legacy 2FA-bound Apple IDs.

This guide is **not**:

- an authorization mechanism to generate API keys with Account Holder (Admin) root privileges
- a substitute for securing `.p8` private key files in secret vaults
- a guide to using legacy username/password authentication for Transporter CLI

---

# 1. App Store Connect API Key Architecture

App Store Connect API Keys (`AuthKey_[KEY_ID].p8`) utilize JWT token authentication to authorize Fastlane, Transporter CLI, and EAS Submit without requiring 2FA interaction.

```text
┌────────────────────────────────────────────────────────┐
│             APP STORE CONNECT API KEY ARCHITECTURE     │
│                                                        │
│  - Key ID: `2X997B2844` (10-character key identifier)  │
│  - Issuer ID: `69a6de70-0000-0000-0000-000000000000`   │
│  - Private Key File: `AuthKey_2X997B2844.p8`           │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Generates Short-Lived JWT Tokens for App Store Connect API ]
```

---

# 2. Key Role-Based Access Control (RBAC) Settings

- **App Manager**: Recommended role for CI/CD upload automation. Allows uploading builds, managing TestFlight, editing metadata, and creating version releases.
- **Developer**: Permits build uploads and TestFlight management, but cannot submit for App Review.
- **Account Holder / Admin**: **FORBIDDEN** for automated CI pipelines.

---

# 3. Operational Verification Checklist

- [ ] **`.p8` Key Downloaded Once**: Key downloaded during generation and secured in secret manager (cannot be re-downloaded from Apple).
- [ ] **App Manager Role Assigned**: Minimum required role ("App Manager") assigned to API key.
- [ ] **CI Secrets Configured**: `APP_STORE_CONNECT_API_KEY`, `KEY_ID`, and `ISSUER_ID` injected into CI runner.

---

# 4. Official Sources

- Apple Creating API Keys for App Store Connect: https://developer.apple.com/documentation/appstoreconnectapi/creating_api_keys_for_app_store_connect_api

---

**Last verified:** August 14, 2026

---

# Related documentation

### iOS signing

- `signing/ios/README.md`
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
