# Build Failures Triage & Compilation Diagnostics

This document details diagnostic workflows, Gradle compile errors, CocoaPods dependency resolution, Metro bundler syntax failures, and Target API 36 mismatches for **Build Failures** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for diagnosing and fixing build compilation failures.

This guide is **not**:

- an authorization mechanism to randomly edit `Podfile.lock` or `build.gradle` without inspecting error logs
- a substitute for verifying Node/npm dependency compatibility
- a guide to disabling strict compiler checks

---

# 1. Diagnostic Flow for Build Failures

```text
[ Build Fails in Terminal / CI Runner ]
                   │
                   ▼
┌────────────────────────────────────────────────────────┐
│             LOG EXTRACTION & CLASSIFICATION            │
│  - Inspect full build log output                       │
│  - Identify layer: iOS CocoaPods vs Android Gradle vs  │
│    JS Metro Bundler syntax error                       │
└──────────────────────────┬─────────────────────────────┘
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
[ iOS Pod Error ] [ Android Gradle ] [ JS Metro ]
Clean Pods &      Check Target SDK    Fix syntax &
re-install        36 & Java 17        clear cache
```

---

# 2. Common Build Error Scenarios & Resolutions

### Scenario A: Android Gradle `targetSdkVersion` Mismatch Error

- **Symptom**: `Manifest merger failed: targetSdkVersion 35 cannot be lower than 36 required by Google Play Console`.
- **Root Cause**: `android/app/build.gradle` has `targetSdkVersion` set below 36.
- **Resolution**:
  1. Open `android/app/build.gradle` or `app.json`.
  2. Set `targetSdkVersion 36` and `compileSdkVersion 36`.
  3. Run `./gradlew clean` and re-compile.

### Scenario B: iOS CocoaPods Specs Synchronization Failure

- **Symptom**: `[!] CocoaPods could not find compatible versions for pod "React-Core"`.
- **Root Cause**: Outdated CocoaPods spec repository or mismatched `Podfile.lock`.
- **Resolution**:
  ```bash
  cd ios && pod deintegrate
  pod repo update
  pod install --repo-update
  ```

---

# 3. Operational Verification Checklist

- [ ] **Full Log Inspected**: Full un-truncated build log extracted and error line identified.
- [ ] **Clean Build Executed**: Local cache cleared (`npx expo start -c` or `./gradlew clean`).
- [ ] **Dependencies Compatible**: All npm packages compatible with Expo SDK 51+ / React Native 0.74+.
- [ ] **Build Exit Code 0**: Build command completes with clean exit code 0.

---

# 4. Official Sources

- Android Build Troubleshooting: https://developer.android.com/studio/build/troubleshooting
- CocoaPods Troubleshooting: https://guides.cocoapods.org/using/troubleshooting.html

---

**Last verified:** August 14, 2026

---

# Related documentation

### Troubleshooting

- `troubleshooting/README.md`
- `troubleshooting/app-not-visible.md`
- `troubleshooting/archive-fails.md`
- `troubleshooting/ci-fails.md`
- `troubleshooting/crash-after-release.md`
- `troubleshooting/deep-links-broken.md`
- `troubleshooting/metadata-rejected.md`
- `troubleshooting/payments-broken.md`
- `troubleshooting/privacy-rejected.md`
- `troubleshooting/push-notifications-broken.md`
- `troubleshooting/review-rejected.md`
- `troubleshooting/signing-fails.md`
- `troubleshooting/upload-fails.md`

### Signing

- `signing/README.md`

### Publishing

- `publishing/README.md`

### Store operations

- `store-operations/rejection-handling.md`

### Post-release

- `post-release/crash-analysis.md`
- `post-release/monitoring.md`

### Testing

- `testing/README.md`
