# Data Collection Taxonomy & Privacy Manifests Handbook

This handbook details the data collection taxonomy, minimization principles, Apple Privacy Manifests (`PrivacyInfo.xcprivacy`), Required Reason APIs, and Google Play Data Safety mapping for **Data Collection** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit, declare, and minimize data collection across application code and third-party SDKs.

This guide is **not**:

- an authorization mechanism to collect un-needed background user data
- a substitute for inspecting compiled binary privacy manifests
- a guide to misrepresenting data collection purposes

---

# 1. Binary Privacy Manifest Architecture (`PrivacyInfo.xcprivacy`)

Apple requires all iOS binaries and compiled third-party SDKs to include a `PrivacyInfo.xcprivacy` manifest declaring data collection and Required Reason API usage.

```xml
<!-- Example: PrivacyInfo.xcprivacy -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.plist">
<plist version="1.0">
<dict>
    <key>NSPrivacyTracking</key>
    <false/>
    <key>NSPrivacyAccessedAPITypes</key>
    <array>
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryUserDefaults</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>CA92.1</string>
            </array>
        </dict>
    </array>
</dict>
</plist>
```

---

# 2. Required Reason API Categories (2026)

Apple enforces mandatory justification for accessing four sensitive system API categories:

1. **User Defaults (`NSPrivacyAccessedAPICategoryUserDefaults`)**: Accessing `NSUserDefaults` to store app settings.
2. **File Modification Timestamps (`NSPrivacyAccessedAPICategoryFileTimestamp`)**: Checking file creation dates.
3. **System Boot Time (`NSPrivacyAccessedAPICategorySystemBootTime`)**: Checking device uptime.
4. **Disk Space (`NSPrivacyAccessedAPICategoryDiskSpace`)**: Checking available storage.

---

# 3. Operational Verification Checklist

- [ ] **Xcode Privacy Report Generated**: Xcode Privacy Report inspected to verify all bundled SDK privacy manifests.
- [ ] **Required Reason APIs Declared**: `PrivacyInfo.xcprivacy` includes reason codes for User Defaults API.
- [ ] **Data Minimization Audited**: Only data necessary for core app features is collected.

---

# 4. Official Sources

- Apple Describing Use of Required Reason API: https://developer.apple.com/documentation/bundleresources/describing_use_of_required_reason_api

---

**Last verified:** August 14, 2026
