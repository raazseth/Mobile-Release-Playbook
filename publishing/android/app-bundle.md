# Android App Bundle (.aab), R8 Obfuscation & Target API 36

This document details the compilation architecture, Android App Bundle (`.aab`) format, Target API Level 36 (Android 16) enforcement, R8/ProGuard code obfuscation, Play App Signing, and Gradle configuration for **Android Builds** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to generate optimized, secure, production-ready `.aab` archives for Google Play Store distribution.

This guide is **not**:

- an authorization mechanism to upload raw `.apk` binaries to Google Play Console
- a guide to disabling R8 code obfuscation on production builds
- a substitute for configuring Google Play App Signing upload keys

---

# 1. Android App Bundle (.aab) Architecture

The Android App Bundle (`.aab`) is Google Play's mandatory publishing format. Unlike legacy APKs, an `.aab` defers final APK generation and signing to Google Play, which builds custom, optimized APKs tailored to each user's specific device architecture (arm64-v8a, armeabi-v7a, x86_64), screen density, and language.

```text
┌────────────────────────────────────────────────────────┐
│             DEVELOPER COMPILATION (`eas build`)        │
│  - Compiles React Native Hermes JS + Native C++/Java   │
│  - Targets Android 16 (API Level 36)                   │
│  - Signs `.aab` archive with Upload Key Keystore      │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼ (Upload `.aab` to Google Play)
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY DYNAMIC DELIVERY               │
│  - Generates Split APKs per device configuration       │
│  - Signs delivered APKs with Master App Signing Key    │
│  - Delivers tailored 30-50% smaller APK to end user   │
└────────────────────────────────────────────────────────┘
```

---

# 2. Target SDK API Level 36 (Android 16) Enforcement

Google Play Console enforces a mandatory annual Target SDK requirement:

```groovy
// android/app/build.gradle
android {
    compileSdkVersion 36
    buildToolsVersion "36.0.0"

    defaultConfig {
        applicationId "com.example.mobileapp"
        minSdkVersion 24       // Android 7.0 (Nougat)
        targetSdkVersion 36    // Target Android 16 (Mandatory in 2026)
        versionCode 143
        versionName "1.2.0"
    }
}
```

> **PUBLISHING GATE**: As of August 31, 2026, Google Play Console rejects any new app or app update compiled with `targetSdkVersion` below **36**.

---

# 3. R8 / ProGuard Code Obfuscation & Resource Shrinking

R8 is Android's default code optimizer and obfuscator. It converts human-readable Java/Kotlin class and method names into short, obfuscated identifiers while removing unused code and resources.

```groovy
// android/app/build.gradle
android {
    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.release
        }
    }
}
```

### React Native & Hermes ProGuard Preservations (`proguard-rules.pro`)

```proguard
# Preserve React Native Native Modules
-keepclassmembers class * extends com.facebook.react.bridge.JavaOnlyArray { *; }
-keepclassmembers class * extends com.facebook.react.bridge.ReactContextBaseJavaModule { *; }

# Preserve Hermes JS Engine symbols
-keep class com.facebook.hermes.unicode.** { *; }
-keep class com.facebook.jni.** { *; }
```

---

# 4. Google Play App Signing & Upload Key Setup

Google Play App Signing separates key management into two distinct cryptographic keys:

1. **Upload Key**: Generated locally by the developer to sign the `.aab` archive. If lost, the Upload Key can be reset by submitting a request to Google Play Support.
2. **App Signing Key**: Stored securely in Google's high-security infrastructure. Used by Google Play to sign the final split APKs delivered to end users.

```bash
# Generate Release Upload Key Keystore via CLI
keytool -genkey -v -keystore release-upload-key.keystore -alias upload-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

---

# 5. Operational Verification Checklist

- [ ] **Target SDK API 36 Set**: `targetSdkVersion 36` configured in `build.gradle` / `app.json`.
- [ ] **`.aab` Format Exported**: Build artifact generated as `.aab` file, not `.apk`.
- [ ] **R8 Minification Active**: `minifyEnabled true` and `shrinkResources true` enabled for release build type.
- [ ] **ProGuard Rules Verified**: `proguard-rules.pro` includes Hermes and React Native preservation rules.
- [ ] **Mapping File Exported**: R8 `mapping.txt` uploaded to Sentry / Firebase Crashlytics for symbolication.

---

# 6. Related Documentation

- [Play Console Handbook](play-console.md) - Play Console management.
- [Production Release Handbook](production-release.md) - Staged rollouts.
- [Signing Android Handbook](../../signing/android/keystore.md) - Keystore security.

---

# 7. Official Sources

- Android Target API Level Requirements: https://developer.android.com/google/play/requirements/target-sdk
- Android App Bundle Documentation: https://developer.android.com/guide/app-bundle
- Shrink, Obfuscate, and Optimize App: https://developer.android.com/build/shrink-code

---

**Last verified:** August 14, 2026
