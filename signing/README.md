# Code Signing & Cryptographic Credential Management Handbook

This directory contains production handbook guides, cryptographic certificate management protocols, iOS Provisioning Profile workflows, Fastlane Match sync rules, Android Keystore management, and Google Play App Signing architectures for **Code Signing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to manage Apple Developer certificates, App Store Connect API keys, Android release keystores, and CI/CD secret injection securely.

This guide is **not**:

- an authorization mechanism to commit un-encrypted `.p12` certificates or keystore files to public git repositories
- a substitute for using Google Play App Signing and Apple Fastlane Match
- a guide to sharing raw developer passwords across teams

---

# 1. Architecture of Mobile Code Signing & Identity Verification

Code signing attaches a cryptographic digital signature to compiled mobile binaries (`.ipa` and `.aab`), verifying binary integrity and developer entity identity.

```text
┌────────────────────────────────────────────────────────┐
│             MOBILE CODE SIGNING ARCHITECTURE           │
│                                                        │
│  - iOS Signing: Apple Distribution Certificate (.p12) │
│    + Mobile Provisioning Profile (.mobileprovision)    │
│    + App Store Connect API Key (.p8)                   │
│                                                        │
│  - Android Signing: Release Upload Keystore (.keystore)│
│    + Google Play App Signing (KMS / Cloud HSM)         │
│    + APK Signature Scheme v2 / v3 / v4                 │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Signed Binary Executable in Production Stores ]
```

---

# 2. Subsystem Directory Taxonomy

| Signing Subsystem | Primary Focus & Domain Scope | Key Components & Files |
|---|---|---|
| **[README.md](README.md)** | Subsystem root index, signing architecture, and security governance rules. | Master taxonomy and security rules. |
| **[ios/](ios/README.md)** | iOS code signing, Fastlane Match, certificates, profiles, `.p8` keys. | [certificates.md](ios/certificates.md), [provisioning-profiles.md](ios/provisioning-profiles.md), [distribution.md](ios/distribution.md), [api-keys.md](ios/api-keys.md), [key-rotation.md](ios/key-rotation.md). |
| **[android/](android/README.md)** | Android keystore, Google Play App Signing, upload keys, key rotation. | [keystore.md](android/keystore.md), [upload-key.md](android/upload-key.md), [play-app-signing.md](android/play-app-signing.md), [key-rotation.md](android/key-rotation.md). |
| **[security/](security/README.md)** | Secret storage vaults, CI/CD secret injection, RBAC, key recovery. | [secret-storage.md](security/secret-storage.md), [ci-secrets.md](security/ci-secrets.md), [access-control.md](security/access-control.md), [recovery.md](security/recovery.md). |

---

# 3. Universal Code Signing Governance Rules

All code signing implementations in this playbook must adhere to five mandatory governance rules:

```text
1. Zero Un-Encrypted Secret Repository Commits
   Raw `.p12` certificates, `.keystore` files, `.p8` API keys, and plain-text passwords MUST NEVER be
   committed to git repositories in un-encrypted format.

2. Mandatory Google Play App Signing Strategy
   All Android applications MUST utilize Google Play App Signing, ensuring Google securely manages the
   master app signing key in Cloud HSM while developers maintain separate upload keys.

3. Centralized Fastlane Match Certificate Management
   iOS team signing MUST use Fastlane Match (`fastlane match appstore`) with an encrypted git repository
   or S3 backend to maintain a single source of truth for certificates and profiles.

4. 90-Day Pre-Expiration Certificate Rotation
   Distribution certificates and provisioning profiles MUST be audited and renewed 90 days prior to
   expiration to prevent unexpected build breaks in CI/CD pipelines.

5. Strictly Scoped API Key Permissions
   App Store Connect API keys (`.p8`) and GCP Service Account JSON keys MUST be created with the
   minimum required role permissions (e.g., App Manager or Developer, never Organization Admin).
```

---

# 4. Related Repository Documentation

- [iOS Publishing Handbook](../publishing/ios/README.md) - iOS build upload.
- [Android Publishing Handbook](../publishing/android/README.md) - Android AAB upload.
- [CI Secret Protection Handbook](../ai/security/secret-protection.md) - AI secret protection.

---

# 5. Official Sources

- Apple Code Signing Guide: https://developer.apple.com/support/code-signing/
- Android App Signing Overview: https://developer.android.com/studio/publish/app-signing

---

**Last verified:** August 14, 2026

