# iOS Build Compilation, Archive Upload & API Keys

This document covers the `.ipa` compilation pipeline, Xcode archive generation, App Store Connect API Key (`.p8`) authentication, and upload commands for **iOS Build Uploads** in Expo and React Native applications — how to compile and upload production-ready `.ipa` archives to App Store Connect without hitting upload rejections or credential failures.

This guide is **not**:

- an authorization mechanism to embed `.p8` private keys in git repositories
- a guide to using deprecated Xcode `altool` upload commands
- a substitute for valid Apple Distribution Signing Certificates and Provisioning Profiles

---

# 1. iOS Build Upload Pipeline

Compiling and uploading an iOS app involves building a signed `.ipa` distribution archive and transferring it to Apple's ingestion servers via the App Store Connect API.

```text
eas build → signed .ipa (Apple Distribution Certificate & Provisioning Profile)
        │
        ↓ (App Store Connect API Key .p8)
Upload via Transporter / EAS Submit / Fastlane → App Store Connect binary validation & symbol ingestion
        │
        ↓
Processing complete → build appears in TestFlight
```

---

# 2. App Store Connect API Key (`.p8`) Authentication Setup

Apple has fully deprecated password-based authentication and legacy `altool` commands. Automated build tools MUST use **App Store Connect API Keys**:

1. **Generate Key**: Log in to App Store Connect -> Users and Access -> Keys -> Generate API Key (Role: **App Manager** or **Admin**).
2. **Key Parameters**:
   - **Key ID**: 10-character string (e.g., `2X9R49336D`).
   - **Issuer ID**: 36-character UUID string (e.g., `69a6de71-7034-47e3-e053-5b8c7c11a4d1`).
   - **Private Key File**: `AuthKey_2X9R49336D.p8`.

> **SECURITY MANDATE**: Apple permits downloading the `.p8` key file **ONCE**. Store `.p8` files in an encrypted CI secret vault (`ASC_API_KEY`). Never commit `.p8` files to git repositories.

---

# 3. Transporter CLI & Fastlane Pilot Commands

Upload compiled `.ipa` archives via **Transporter** (Apple's official upload app — install from the Mac App Store; its bundled CLI, `iTMSTransporter`, is being folded into the app and its flags have changed recently, so verify the exact invocation against current Apple documentation before scripting it) or **Fastlane Pilot**:

```bash
# Upload .ipa archive using Fastlane Pilot with ASC API Key
bundle exec fastlane pilot upload \
  --api_key '{"key_id":"2X9R49336D","issuer_id":"69a6de71-7034-47e3-e053-5b8c7c11a4d1","key_filepath":"./credentials/AuthKey_2X9R49336D.p8"}' \
  --ipa "./build/output.ipa" \
  --skip_waiting_for_build_processing true
```

---

# 4. Operational Verification Checklist

- [ ] **Distribution Profile Valid**: Signed with active Apple Distribution Certificate and Production Profile.
- [ ] **`.p8` Key Authenticated**: App Store Connect API key verified functional with `App Manager` role.
- [ ] **`ITSAppUsesNonExemptEncryption` Included**: `Info.plist` includes export compliance key.
- [ ] **Build Number Incremented**: `CFBundleVersion` (`buildNumber`) strictly incremented over previous upload.
- [ ] **Build Processing Complete**: Build transitions from *Processing* to *Ready to Test* in TestFlight.

---

# Related documentation

### Publishing (iOS)

- `publishing/ios/README.md`
- `publishing/ios/app-review.md`
- `publishing/ios/app-store-connect.md`
- `publishing/ios/metadata.md`
- `publishing/ios/production-release.md`
- `publishing/ios/screenshots.md`
- `publishing/ios/testflight.md`

### Store accounts

- `store-accounts/app-store-connect.md`

### iOS signing

- `signing/ios/README.md`
- `signing/ios/distribution.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/ios.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- App Store Connect API Keys: https://developer.apple.com/documentation/appstoreconnectapi/creating_api_keys_for_app_store_connect_api
- Uploading Builds with Transporter: https://developer.apple.com/help/app-store-connect/#/devb1c6762e2

---

**Last verified:** August 14, 2026

