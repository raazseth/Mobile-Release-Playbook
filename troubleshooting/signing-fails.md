# Code Signing & Keystore Failures Triage

This document details diagnostic workflows, Apple Provisioning Profile expirations, certificate Bundle ID mismatches (`ITMS-90161`), and Android Release Upload Keystore password errors for **Signing Failures** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for resolving code signing failures.

This guide is **not**:

- an authorization mechanism to embed un-encrypted keystores in public git repositories
- a substitute for managing Apple Distribution Certificates
- a guide to disabling code signing on release binaries

---

# 1. Code Signing Diagnostic Architecture

Code signing validates that a compiled mobile binary originates from an authenticated developer entity.

```text
┌────────────────────────────────────────────────────────┐
│             CODE SIGNING FAILURE DIAGNOSTIC            │
│                                                        │
│  - iOS: Certificate, Provisioning Profile, & Bundle ID │
│  - Android: Release Keystore, Alias, & Password        │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ iOS Provisioning Error ]          [ Android Keystore Password ]
Match Profile & Bundle ID           Verify `KEYSTORE_PASSWORD` in
in Apple Developer Portal           EAS Secrets / `gradle.properties`
```

---

# 2. Key Code Signing Errors & Solutions

### Error 1: iOS `ITMS-90161: Invalid Provisioning Profile`
- **Root Cause**: The provisioning profile attached to the build does not include the target App ID or has expired.
- **Resolution**:
  1. Regenerate provisioning profile in Apple Developer Portal under Profiles.
  2. Sync certificates using Fastlane Match (`fastlane match appstore`).
  3. Recompile and re-sign the `.ipa` binary.

### Error 2: Android `Keystore password was incorrect`
- **Root Cause**: `MYAPP_RELEASE_STORE_PASSWORD` environment variable does not match the actual keystore password.
- **Resolution**:
  1. Verify keystore password locally via `keytool -list -v -keystore release-upload-key.keystore`.
  2. Update secret in EAS Secrets (`eas secret:create`) or CI environment variables.

---

# 3. Operational Verification Checklist

- [ ] **Provisioning Profile Active**: Profile is active in Apple Developer Portal and matches Bundle ID.
- [ ] **Distribution Certificate Valid**: Apple Distribution Certificate has not expired.
- [ ] **Android Keystore Password Verified**: Keystore password verified via `keytool`.
- [ ] **Signing Exit Code 0**: Xcode / Gradle signing step finishes without error.

---

# 4. Official Sources

- Apple Code Signing Guidance: https://developer.apple.com/support/code-signing/
- Android App Signing Guide: https://developer.android.com/studio/publish/app-signing

---

**Last verified:** August 14, 2026

---

# Related documentation

### Troubleshooting

- `troubleshooting/README.md`
- `troubleshooting/app-not-visible.md`
- `troubleshooting/archive-fails.md`
- `troubleshooting/build-fails.md`
- `troubleshooting/ci-fails.md`
- `troubleshooting/crash-after-release.md`
- `troubleshooting/deep-links-broken.md`
- `troubleshooting/metadata-rejected.md`
- `troubleshooting/payments-broken.md`
- `troubleshooting/privacy-rejected.md`
- `troubleshooting/push-notifications-broken.md`
- `troubleshooting/review-rejected.md`
- `troubleshooting/upload-fails.md`

### Signing

- `signing/README.md`

### Publishing

- `publishing/README.md`

### Store operations

- `store-operations/rejection-handling.md`

### Post-release

- `post-release/crash-analysis.md`
- `post-release/monitoring.md`

### Testing

- `testing/README.md`
