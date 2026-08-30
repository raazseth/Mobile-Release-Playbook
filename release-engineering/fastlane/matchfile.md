# Fastlane `Matchfile` Setup Handbook

This handbook details Fastlane `Matchfile` configuration, `git_url` repository paths, `storage_mode` backends (git, s3, google_cloud), `app_identifier` arrays, and decryption key management for **Fastlane Matchfile** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to configure Fastlane Match certificate synchronization.

This guide is **not**:

- an authorization mechanism to store un-encrypted Match repositories
- a substitute for using S3 or git cloud storage backends
- a guide to hardcoding OpenSSL match passwords in `Matchfile`

---

# 1. Production `Matchfile` Configuration Example

```ruby
# fastlane/Matchfile

git_url("git@github.com:example-org/mobile-certificates-match.git")
storage_mode("git")

type("appstore") # Default profile type

app_identifier(["com.example.mobileapp", "com.example.mobileapp.staging"])
username("developer@example.com")

# S3 Backend Example (Alternative to Git)
# storage_mode("s3")
# s3_bucket("my-company-mobile-certificates-s3")
# s3_region("us-east-1")
```

---

# 2. Key Matchfile Storage Backends

1. **Git Repository Storage (`git`)**: Stores encrypted `.p12` certificates and `.mobileprovision` profiles in a private git repo encrypted via OpenSSL.
2. **Amazon S3 Storage (`s3`)**: Stores encrypted certificates in an AWS S3 bucket with KMS encryption.
3. **Google Cloud Storage (`google_cloud`)**: Stores encrypted certificates in a GCP Storage Bucket.

---

# 3. Operational Verification Checklist

- [ ] **Storage Mode Configured**: `storage_mode` set to `"git"`, `"s3"`, or `"google_cloud"`.
- [ ] **App Identifiers Listed**: Array includes all bundle IDs for production and staging apps.
- [ ] **Passphrase Protected**: OpenSSL match passphrase stored securely in CI secrets (`MATCH_PASSWORD`).

---

# 4. Official Sources

- Fastlane Matchfile Reference: https://docs.fastlane.tools/actions/match/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Fastlane

- `release-engineering/fastlane/README.md`
- `release-engineering/fastlane/appfile.md`
- `release-engineering/fastlane/fastfile.md`

### React Native

- `frameworks/react-native/fastlane.md`

### Release engineering

- `release-engineering/build-systems.md`

### iOS signing

- `signing/ios/README.md`
