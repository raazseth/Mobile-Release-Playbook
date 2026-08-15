# App Store Connect Administration & API Keys

This document details the portal administration, user invitation workflows, App Store Connect API Key management (`.p8` private keys), TestFlight beta distribution, and sandbox tester configuration for **App Store Connect**.

Engineered in alignment with **2026 platform specifications**, it specifies how to securely manage App Store Connect access for iOS development teams, CI/CD automated build toolchains (Fastlane Deliver, EAS Submit), and external QA testers.

This guide is **not**:

- an authorization mechanism to share App Store Connect API Keys over insecure channels
- a replacement for Account Holder agreement sign-offs (see [agreements.md](agreements.md))
- a substitute for configuring TestFlight beta review information

---

# 1. App Store Connect Portal Overview

App Store Connect is Apple's web portal and API suite for managing iOS app metadata, uploading build binaries, configuring in-app purchases, conducting TestFlight beta testing, and viewing sales/analytics reports.

```text
┌────────────────────────────────────────────────────────┐
│                   APP STORE CONNECT                    │
│                                                        │
│  - App Metadata & Store Listings                       │
│  - TestFlight Beta Distribution                        │
│  - In-App Purchase & Subscription Configurations       │
│  - App Store Connect API Keys (.p8)                    │
│  - Financial Agreements, Taxes & Payout Reports        │
└──────────────────────────┬─────────────────────────────┘
                           │
    ┌──────────────────────┴──────────────────────┐
    ▼                                             ▼
┌────────────────────────┐               ┌────────────────────────┐
│ Human Team Users       │               │ Automated CI/CD Tool   │
│ (RBAC Role Access)     │               │ (App Store Connect API)│
└────────────────────────┘               └────────────────────────┘
```

---

# 2. App Store Connect API Keys (`.p8`) Management

Automated tools (Fastlane, EAS Submit, App Store Connect API scripts) authenticate using **App Store Connect API Keys**.

```text
App Store Connect ──→ [ Users and Access → Keys ] ──→ [ Generate API Key ]
                                                            │
                                                            ▼
                                           [ Download `.p8` Key File (ONE-TIME ONLY) ]
                                                            │
                                                            ▼
                                           [ Store Key in Managed CI Secret Vault ]
```

### Key Generation Parameters

- **Key ID**: 10-character alphanumeric string (e.g., `2X9R49336D`).
- **Issuer ID**: 36-character UUID representing your App Store Connect account.
- **Access Role**: Select **App Manager** or **Admin** for build deployment tools. Select **Developer** for read-only status tools.

> **SECURITY MANDATE**: Apple allows downloading the `.p8` private key file **ONCE**. Store the `.p8` file immediately in an encrypted secrets vault (e.g., AWS Secrets Manager, GitHub Secrets). Never commit `.p8` files to git repositories.

---

# 3. TestFlight Beta Distribution Workflows

App Store Connect manages internal and external beta testing via TestFlight:

```text
[ Binary Uploaded to TestFlight ]
               │
               ▼
┌────────────────────────────────────────────────────────┐
│             TESTFLIGHT DISTRIBUTION TRACKS             │
│                                                        │
│  1. Internal Testing: Immediate distribution to up to  │
│     100 internal team members (No Export Compliance    │
│     review required if encryption declared in Plist).  │
│                                                        │
│  2. External Testing: Requires Beta App Review by      │
│     Apple. Supports up to 10,000 external testers      │
│     via email invite or public link.                   │
└────────────────────────────────────────────────────────┘
```

---

# 4. Operational Verification Checklist

- [ ] **API Keys Isolated**: `.p8` private keys stored exclusively in CI secret storage; absent from repository commits.
- [ ] **Minimal Role Assigned**: CI API keys assigned **App Manager** role rather than full Admin where feasible.
- [ ] **Encryption Declared**: `ITSAppUsesNonExemptEncryption` declared in `Info.plist` to skip TestFlight export compliance prompts.
- [ ] **Sandbox Testers Created**: Dedicated App Store Connect Sandbox Testers created for IAP testing.
- [ ] **2FA Enforced for Users**: All invited team members have active 2FA on their Apple Accounts.

---

# 5. Related Documentation

- [Apple Developer Handbook](apple-developer.md) - Program enrollment.
- [Roles & Permissions Handbook](roles-and-permissions.md) - Permission matrices.
- [Secret Protection](../ai/security/secret-protection.md) - Masking keys.

---

# 6. Official Sources

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- App Store Connect API Keys: https://developer.apple.com/documentation/appstoreconnectapi/creating_api_keys_for_app_store_connect_api

---

**Last verified:** August 14, 2026
