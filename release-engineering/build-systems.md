# Native & JavaScript Build Systems Handbook

This handbook details Xcode Archive CLI (`xcodebuild`), Android Gradle compilation (`./gradlew bundleRelease`), Metro bundler optimization, Hermes bytecode compilation, and cache management for **Build Systems** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to configure native build toolchains for production releases.

This guide is **not**:

- an authorization mechanism to compile debug builds for production store submission
- a substitute for configuring Hermes JS engine bytecode compilation
- a guide to disabling R8 code minification on Android

---

# 1. Native Build Toolchain Architecture

React Native release builds require a dual compilation pipeline: compiling JavaScript code into optimized Hermes bytecode, and compiling native C++/Objective-C/Java/Kotlin code into signed OS executables.

```text
┌────────────────────────────────────────────────────────┐
│             NATIVE BUILD TOOLCHAIN PIPELINE            │
│                                                        │
│  - JS Bundling: Metro ──→ Hermes Bytecode (`.bundle`)  │
│  - iOS Native: `xcodebuild -workspace ... archive`    │
│  - Android Native: `./gradlew bundleRelease` (R8)     │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ iOS `.ipa` Binary Archive ]    [ Android `.aab` Bundle ]
```

---

# 2. Key Build Execution Commands

### iOS Production Archive (`xcodebuild`)

```bash
# Archive iOS release candidate build via CLI
xcodebuild -workspace ios/MobileApp.xcworkspace \
  -scheme MobileApp \
  -configuration Release \
  -sdk iphoneos \
  -archivePath ./build/MobileApp.xcarchive \
  archive
```

### Android Production App Bundle (`./gradlew`)

```bash
# Compile optimized Android App Bundle (.aab) with R8 minification
cd android && ./gradlew bundleRelease --no-daemon
```

---

# 3. Operational Verification Checklist

- [ ] **Hermes Bytecode Active**: Hermes engine (`jsEngine: "hermes"`) verified active in `app.json`.
- [ ] **R8 Minification Enabled**: `minifyEnabled true` enabled in `android/app/build.gradle`.
- [ ] **Clean Build Executed**: `./gradlew clean` and Xcode derived data cleared before archiving.

---

# 4. Official Sources

- React Native Performance & Hermes: https://reactnative.dev/docs/hermes
- Android Gradle Plugin User Guide: https://developer.android.com/build

---

**Last verified:** August 14, 2026
