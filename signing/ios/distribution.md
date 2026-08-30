# Fastlane Match & Automated Cloud Signing Handbook

This handbook details automated iOS certificate synchronization using Fastlane Match (`fastlane match appstore`), encrypted git repositories, S3 / Google Cloud Storage match backends, and CI/CD automated signing for **iOS Distribution** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to maintain a single cryptographic source of truth across engineering teams and CI runners.

This guide is **not**:

- an authorization mechanism to manage iOS certificates manually across individual laptops
- a substitute for encrypting Fastlane Match git repositories with a strong passphrase
- a guide to running `fastlane match nuke` without team authorization

---

# 1. Fastlane Match Architecture & Sync Pipeline

Fastlane Match encrypts certificates and provisioning profiles using OpenSSL AES-256-CBC, storing them in a central private git repository or S3 bucket.

```text
┌────────────────────────────────────────────────────────┐
│             FASTLANE MATCH SYNC PIPELINE               │
│                                                        │
│  [ Encrypted Git Repo / S3 Match Storage Bucket ]      │
│                         │                              │
│                         ▼ (AES-256 OpenSSL Decryption)  │
│  [ Fastlane Match CLI (`fastlane match appstore`) ]    │
│                         │                              │
│             ┌───────────┴───────────┐                  │
│             ▼                       ▼                  │
│  [ Local Dev Laptop ]     [ CI/CD Runner (EAS/GHA) ]   │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Fastlane Match CLI Commands

```bash
# Sync production App Store certificates & profiles
fastlane match appstore --readonly

# Sync development certificates & profiles for device testing
fastlane match development --readonly

# Generate new certificates & profiles if expired
fastlane match appstore
```

---

# 3. Operational Verification Checklist

- [ ] **Match Repo Encrypted**: Git repository encrypted via OpenSSL AES-256 with strong passphrase stored in secret vault.
- [ ] **Read-Only Mode in CI**: CI/CD pipelines execute `fastlane match` with `--readonly` flag to prevent unauthorized certificate generation.
- [ ] **S3 Backend Active**: S3 or Google Cloud Storage bucket configured for cloud storage backend.

---

# 4. Official Sources

- Fastlane Match Documentation: https://docs.fastlane.tools/actions/match/

---

**Last verified:** August 14, 2026

---

# Related documentation

### iOS signing

- `signing/ios/README.md`
- `signing/ios/api-keys.md`
- `signing/ios/certificates.md`
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
