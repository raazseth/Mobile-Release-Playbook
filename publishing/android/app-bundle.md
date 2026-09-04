# Android App Bundle, R8, and the Target API Level

This covers the `.aab` format, R8/ProGuard obfuscation, Play App Signing, and the Gradle config you need to produce a production-ready Android App Bundle.

This guide is **not**:

- an authorization mechanism to upload a raw `.apk` to Google Play Console
- a guide to disabling R8 obfuscation on production builds
- a substitute for setting up Google Play App Signing upload keys

---

## 1. Why `.aab` instead of `.apk`

The Android App Bundle (`.aab`) is Google Play's required publishing format. Unlike a legacy APK, an `.aab` doesn't contain a finished, installable app — it defers final APK generation and signing to Google Play, which builds a custom, optimized APK for each user's specific device architecture (arm64-v8a, armeabi-v7a, x86_64), screen density, and language.

```text
eas build → .aab signed with the upload key keystore, targeting the current required API level
        │
        ↓ (upload .aab to Google Play)
Google Play dynamic delivery
  - generates split APKs per device configuration
  - re-signs delivered APKs with the app signing key
  - delivers a smaller, device-tailored APK to each user
```

## 2. Target API level

Google Play enforces a mandatory annual target SDK bump:

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

> **Note:** From August 31, 2026, Google Play requires new apps and updates to target API level 36 (Android 16) or higher; existing apps need API level 35+ to stay visible to new users on newer devices. You can request an extension to November 1, 2026. This requirement moves every year — check the current target level against [Google's target API level page](https://support.google.com/googleplay/android-developer/answer/11926878) before relying on the number `36` above.

## 3. R8 obfuscation and resource shrinking

R8 is Android's default code optimizer and obfuscator. It turns readable Java/Kotlin class and method names into short, obfuscated identifiers, and strips out unused code and resources along the way.

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

React Native and Hermes need a few things kept from being obfuscated away, in `proguard-rules.pro`:

```proguard
# Preserve React Native Native Modules
-keepclassmembers class * extends com.facebook.react.bridge.JavaOnlyArray { *; }
-keepclassmembers class * extends com.facebook.react.bridge.ReactContextBaseJavaModule { *; }

# Preserve Hermes JS Engine symbols
-keep class com.facebook.hermes.unicode.** { *; }
-keep class com.facebook.jni.** { *; }
```

## 4. Play App Signing and the upload key

Google Play App Signing splits key management into two keys:

1. **Upload key** — you generate this locally to sign the `.aab`. If you lose it, you can request a reset from Google Play Support.
2. **App signing key** — Google holds this in its own secure infrastructure and uses it to sign the final split APKs delivered to users.

```bash
# Generate a release upload key keystore
keytool -genkey -v -keystore release-upload-key.keystore -alias upload-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

## 5. Before you ship

- [ ] `targetSdkVersion` is set to the current required level in `build.gradle` / `app.json`.
- [ ] The build output is an `.aab`, not an `.apk`.
- [ ] `minifyEnabled true` and `shrinkResources true` are set for the release build type.
- [ ] `proguard-rules.pro` keeps Hermes and React Native's native module classes.
- [ ] The R8 `mapping.txt` file is uploaded to your crash reporter (Sentry / Firebase Crashlytics) so stack traces symbolicate correctly.

---

## Official sources

- Android target API level requirements: https://developer.android.com/google/play/requirements/target-sdk
- Target API level requirements for Google Play apps: https://support.google.com/googleplay/android-developer/answer/11926878
- Android App Bundle documentation: https://developer.android.com/guide/app-bundle
- Shrink, obfuscate, and optimize your app: https://developer.android.com/build/shrink-code

**Last verified:** August 14, 2026

---

## Related documentation

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
