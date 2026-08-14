# Build & Environment Configuration Audit Handbook

This handbook details build configuration audits (`app.json`, `eas.json`), Google Play `targetSdkVersion 36` enforcement, `ITSAppUsesNonExemptEncryption` declarations, and Bundle Identifier matching for **Configuration Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit build configuration settings before initiating binary compilation.

This guide is **not**:

- an authorization mechanism to submit builds targeting obsolete Android SDK levels
- a substitute for verifying bundle identifier consistency across environments
- a guide to misconfiguring encryption export compliance

---

# 1. Configuration Audit Pipeline

```text
┌────────────────────────────────────────────────────────┐
│             CONFIGURATION AUDIT PIPELINE               │
│                                                        │
│  - Verify `targetSdkVersion 36` in `build.gradle`       │
│  - Verify Bundle ID (`com.example.app`) across builds  │
│  - Verify `ITSAppUsesNonExemptEncryption: false`       │
│  - Verify Production API Endpoints in `eas.json`       │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified 100% Configured for Production Build Execution ]
```

---

# 2. Key Configuration Audit Rules (2026)

1. **Android Target SDK 36 (Android 16)**: `targetSdkVersion` MUST be set to `36` in `android/app/build.gradle`.
2. **Export Compliance**: `ITSAppUsesNonExemptEncryption` MUST be set to `false` in `Info.plist` if using standard HTTPS/TLS encryption to bypass export compliance prompts in App Store Connect.
3. **Bundle Identifier Alignment**: iOS `bundleIdentifier` and Android `package` name MUST match store console records exactly (`com.company.appname`).

---

# 3. Operational Verification Checklist

- [ ] **Target SDK 36 Set**: `targetSdkVersion` set to 36 in `build.gradle`.
- [ ] **Export Compliance Declared**: `ITSAppUsesNonExemptEncryption: false` present in `Info.plist`.
- [ ] **Bundle IDs Aligned**: `bundleIdentifier` matches App Store Connect and Play Console 100%.

---

# 4. Official Sources

- Expo App Config Schema: https://docs.expo.dev/versions/latest/config/app/

---

**Last verified:** August 14, 2026
