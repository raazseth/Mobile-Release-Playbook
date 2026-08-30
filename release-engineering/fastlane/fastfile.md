# Fastlane `Fastfile` Lane Architecture Handbook

This handbook details Fastlane `Fastfile` lane architecture, custom lanes (`lane :beta`, `lane :release`), gym packaging, pilot upload, supply deployment, and error handling for **Fastlane Fastfile** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to construct maintainable Fastlane deployment scripts.

This guide is **not**:

- an authorization mechanism to hardcoded credentials in `Fastfile`
- a substitute for using App Store Connect `.p8` API keys
- a guide to ignoring build packaging errors in custom lanes

---

# 1. Production `Fastfile` Lane Architecture Example

```ruby
default_platform(:ios)

platform :ios do
  before_all do
    setup_ci if is_ci
  end

  desc "Push a new beta build to TestFlight"
  lane :beta do
    api_key = app_store_connect_api_key(
      key_id: ENV["APP_STORE_CONNECT_KEY_ID"],
      issuer_id: ENV["APP_STORE_CONNECT_ISSUER_ID"],
      key_content: ENV["APP_STORE_CONNECT_API_KEY_KEY"],
      is_key_content_base64: true
    )

    match(type: "appstore", readonly: is_ci, api_key: api_key)
    build_app(workspace: "ios/MobileApp.xcworkspace", scheme: "MobileApp", configuration: "Release")
    upload_to_testflight(api_key: api_key, skip_waiting_for_build_processing: true)
  end
end
```

---

# 2. Key Fastlane Actions Used

- `setup_ci`: Configures temporary Keychain in CI environments to prevent prompt locks.
- `match`: Fetches encrypted code signing certificates and profiles.
- `build_app` (`gym`): Archives and exports iOS `.ipa` binary package.
- `upload_to_testflight` (`pilot`): Uploads compiled `.ipa` binary to App Store Connect.

---

# 3. Operational Verification Checklist

- [ ] **`setup_ci` Included**: `before_all` hook includes `setup_ci` when running in CI environments.
- [ ] **`match` Executed Read-Only**: `match` executes with `readonly: true` in CI runners.
- [ ] **`.p8` API Key Authenticated**: API key used for `upload_to_testflight`.

---

# 4. Official Sources

- Fastlane Fastfile Reference: https://docs.fastlane.tools/advanced/Fastfile/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Fastlane

- `release-engineering/fastlane/README.md`
- `release-engineering/fastlane/appfile.md`
- `release-engineering/fastlane/matchfile.md`

### React Native

- `frameworks/react-native/fastlane.md`

### Release engineering

- `release-engineering/build-systems.md`

### iOS signing

- `signing/ios/README.md`
