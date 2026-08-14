# Fastlane `Appfile` Configuration Handbook

This handbook details Fastlane `Appfile` configuration, `app_identifier` bundle ID mapping, `apple_id` team parameters, `itc_team_id`, and `json_key_file` paths for **Fastlane Appfile** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to define global app identifiers for Fastlane actions.

This guide is **not**:

- an authorization mechanism to hardcode developer passwords in `Appfile`
- a substitute for scoping bundle IDs per lane
- a guide to using invalid team IDs

---

# 1. Production `Appfile` Configuration Example

```ruby
# fastlane/Appfile

# iOS Configuration
app_identifier("com.example.mobileapp")
apple_id("developer@example.com")
itc_team_id("123456789")
team_id("ABCDE12345")

# Android Configuration
json_key_file("./google-service-account.json")
package_name("com.example.mobileapp")

# Environment Override for Staging Lane
for_lane :beta do
  app_identifier("com.example.mobileapp.staging")
end
```

---

# 2. Key Appfile Parameters

- `app_identifier`: Bundle Identifier matching App Store Connect record (`com.example.mobileapp`).
- `itc_team_id`: App Store Connect Team ID for multi-team Apple Developer Accounts.
- `package_name`: Android package name matching Google Play Console record.

---

# 3. Operational Verification Checklist

- [ ] **Bundle ID Matches App Store**: `app_identifier` matches App Store Connect record 100%.
- [ ] **Package Name Matches Play Console**: `package_name` matches Google Play Console record 100%.
- [ ] **Lane Overrides Configured**: Specific bundle IDs configured for `:beta` and `:release` lanes.

---

# 4. Official Sources

- Fastlane Appfile Reference: https://docs.fastlane.tools/advanced/Appfile/

---

**Last verified:** August 14, 2026
