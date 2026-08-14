# Xcode Archive & .aab Packaging Failures Triage

This document details diagnostic workflows, R8/ProGuard obfuscation crashes, Xcode archiving failures, and Bitcode deprecation errors for **Packaging & Archiving Failures** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for resolving binary archiving failures.

This guide is **not**:

- an authorization mechanism to disable R8 code obfuscation without testing
- a substitute for managing Hermes symbol maps
- a guide to using legacy Bitcode flags (Bitcode is deprecated by Apple)

---

# 1. Archiving Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             PACKAGING FAILURE DIAGNOSTIC               │
│                                                        │
│  - iOS: Xcode Archive export error (`.xcarchive`)       │
│  - Android: `./gradlew bundleRelease` R8 error         │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ R8 ProGuard Class Stripping ]     [ Xcode Bitcode Enabled Error ]
Add `-keep` rule for native         Disable Bitcode in build settings
module in `proguard-rules.pro`      (`ENABLE_BITCODE = NO`)
```

---

# 2. Key Packaging Errors & Resolutions

### Scenario A: Android R8 `ClassNotFoundException` in Release Build
- **Symptom**: App crashes on launch only in release `.aab` builds; works in debug.
- **Root Cause**: R8 minification stripped native module classes accessed via Java reflection.
- **Resolution**: Add explicit preservation rules in `android/app/proguard-rules.pro`:
  ```proguard
  -keep class com.example.nativemodule.** { *; }
  ```

### Scenario B: Xcode `Bitcode is no longer supported`
- **Symptom**: Xcode archive build fails with Bitcode deprecation error.
- **Resolution**: Ensure `ENABLE_BITCODE` is set to `NO` in target build settings.

---

# 3. Operational Verification Checklist

- [ ] **`.ipa` Archive Generated**: Xcode archive completes and generates valid `.ipa` bundle.
- [ ] **`.aab` Bundle Generated**: `./gradlew bundleRelease` completes and generates valid `.aab` bundle.
- [ ] **R8 ProGuard Verified**: Release `.aab` tested on device without reflection crashes.

---

# 4. Official Sources

- Android R8 Shrink Code: https://developer.android.com/build/shrink-code

---

**Last verified:** August 14, 2026
