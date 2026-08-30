# Android App Bundle (.aab), R8 Obfuscation & Target API 36

This document covers the Android App Bundle (`.aab`) format, target API level enforcement, R8/ProGuard code obfuscation, Play App Signing, and Gradle configuration for **Android Builds** in Expo and React Native applications — how to generate optimized, production-ready `.aab` archives for Google Play distribution.

This guide is **not**:

- an authorization mechanism to upload raw `.apk` binaries to Google Play Console
- a guide to disabling R8 code obfuscation on production builds
- a substitute for configuring Google Play App Signing upload keys

---

# 1. Android App Bundle (.aab) Architecture

The Android App Bundle (`.aab`) is Google Play's mandatory publishing format. Unlike legacy APKs, an `.aab` defers final APK generation and signing to Google Play, which builds custom, optimized APKs tailored to each user's specific device architecture (arm64-v8a, armeabi-v7a, x86_64), screen density, and language.

```text
eas build → .aab signed with the upload key keystore, targeting the current required API level
        │
        ↓ (upload .aab to Google Play)
Google Play dynamic delivery
  - generates split APKs per device configuration
  - re-signs delivered APKs with the app signing key
  - delivers a smaller, device-tailored APK to each user
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

> **Note:** From August 31, 2026, Google Play requires new apps and updates to target API level 36 (Android 16) or higher; existing apps need API level 35+ to stay visible to new users on newer devices. Developers can request an extension to November 1, 2026. This requirement moves every year — verify the current target level against [Google's target API level page](https://support.google.com/googleplay/android-developer/answer/11926878) before relying on the number `36` above.

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

# Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-review.md`
- `publishing/android/closed-testing.md`
- `publishing/android/internal-testing.md`
- `publishing/android/metadata.md`
- `publishing/android/play-console.md`
- `publishing/android/production-release.md`
- `publishing/android/screenshots.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Android signing

- `signing/android/README.md`
- `signing/android/play-app-signing.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Android Target API Level Requirements: https://developer.android.com/google/play/requirements/target-sdk
- Target API level requirements for Google Play apps: https://support.google.com/googleplay/android-developer/answer/11926878
- Android App Bundle Documentation: https://developer.android.com/guide/app-bundle
- Shrink, Obfuscate, and Optimize App: https://developer.android.com/build/shrink-code

---

**Last verified:** August 14, 2026

