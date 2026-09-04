# Building and Uploading an iOS Archive

This covers compiling a signed `.ipa`, authenticating with App Store Connect, and uploading the archive — the steps between "I have a working build" and "the build shows up in TestFlight."

This guide is **not**:

- an authorization mechanism to embed a `.p8` private key in a git repository
- a guide to using the deprecated Xcode `altool` upload commands
- a substitute for having a valid Apple Distribution Certificate and Provisioning Profile

---

## 1. The upload pipeline

Building and uploading an iOS app means producing a signed `.ipa` archive and sending it to Apple's ingestion servers through the App Store Connect API.

```text
eas build → signed .ipa (Apple Distribution Certificate & Provisioning Profile)
        │
        ↓ (App Store Connect API Key, .p8)
Upload via Transporter / EAS Submit / Fastlane → App Store Connect binary validation
        │
        ↓
Processing complete → build appears in TestFlight
```

## 2. Setting up an App Store Connect API key

Apple has fully retired password-based authentication and the legacy `altool` commands. Automated build tools need an **App Store Connect API Key**:

1. In App Store Connect, go to Users and Access → Keys → Generate API Key, and give it the **App Manager** or **Admin** role.
2. Note the three values Apple gives you:
   - **Key ID** — a 10-character string (e.g., `2X9R49336D`)
   - **Issuer ID** — a 36-character UUID (e.g., `69a6de71-7034-47e3-e053-5b8c7c11a4d1`)
   - **Private Key File** — `AuthKey_2X9R49336D.p8`

> **Important:** Apple lets you download the `.p8` file exactly once. Store it in an encrypted CI secret (e.g., an `ASC_API_KEY` secret), and never commit it to git.

## 3. Uploading with Transporter or Fastlane Pilot

Upload the compiled `.ipa` with **Transporter** (Apple's official upload app, available on the Mac App Store — its bundled CLI, `iTMSTransporter`, is being folded into the app and its flags have changed recently, so check the exact invocation against current Apple documentation before scripting it) or with **Fastlane Pilot**:

```bash
# Upload .ipa with Fastlane Pilot, using an App Store Connect API key
bundle exec fastlane pilot upload \
  --api_key '{"key_id":"2X9R49336D","issuer_id":"69a6de71-7034-47e3-e053-5b8c7c11a4d1","key_filepath":"./credentials/AuthKey_2X9R49336D.p8"}' \
  --ipa "./build/output.ipa" \
  --skip_waiting_for_build_processing true
```

## 4. Before you upload

- [ ] The build is signed with a valid, current Apple Distribution Certificate and production Provisioning Profile.
- [ ] The App Store Connect API key authenticates successfully and has the App Manager role.
- [ ] `Info.plist` declares `ITSAppUsesNonExemptEncryption`.
- [ ] `CFBundleVersion` (the build number) is higher than every previous upload for this app.
- [ ] The build moves from *Processing* to *Ready to Test* in TestFlight without errors.

---

## Official sources

- App Store Connect API Keys: https://developer.apple.com/documentation/appstoreconnectapi/creating_api_keys_for_app_store_connect_api
- Uploading builds with Transporter: https://developer.apple.com/help/app-store-connect/#/devb1c6762e2

**Last verified:** August 14, 2026

---

## Related documentation

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
