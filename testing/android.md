# Android-Specific Testing & Target API 36 Behavior

This document details Android Emulator automation (`adb`), Firebase Test Lab device matrix scanning, Target API Level 36 (Android 16) behavior verification, and R8/ProGuard obfuscation testing for **Android Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test Android applications systematically on simulated and physical Android hardware.

This guide is **not**:

- an authorization mechanism to skip testing R8 release builds
- a substitute for inspecting Google Play Pre-Launch Reports
- a guide to bypassing Android 14+ / 15+ permission dialog rules

---

# 1. Android Debug Bridge (`adb`) Automation CLI

Automate Android Emulator lifecycle management, APK/AAB installation, deep link triggering, and permission grants via `adb`:

```bash
# List connected Android Emulators / Physical Devices
adb devices

# Install compiled release APK/AAB onto emulator
adb install -r android/app/build/outputs/apk/release/app-release.apk

# Trigger Deep Link Intent
adb shell am start -W -a android.intent.action.VIEW -d "mobileapp://workout/123" com.example.mobileapp

# Revoke Camera Permission to test fallback UI
adb shell pm revoke com.example.mobileapp android.permission.CAMERA

# Capture Android Logcat Crash Trace
adb logcat *:E | grep "ReactAndroid"
```

---

# 2. R8 / ProGuard Obfuscation Testing Protocol

Release builds compiled with `minifyEnabled true` obfuscate class names and strip unused code. Testing release builds is mandatory to catch ProGuard reflection crashes:

```text
┌────────────────────────────────────────────────────────┐
│             R8 OBJECTION & MINIFICATION TESTING        │
│                                                        │
│  1. Compile Release APK/AAB locally with R8 active.    │
│  2. Install Release Build on Android Device.           │
│  3. Test Native Modules (Camera, Geolocation, IAP).    │
│  4. Assert zero `ClassNotFoundException` in Logcat.    │
└────────────────────────────────────────────────────────┘
```

---

# 3. Operational Verification Checklist

- [ ] **Target API 36 Tested**: App tested on Android 16 (API 36) emulator for runtime behavior changes.
- [ ] **R8 Build Verified**: Release build compiled with `minifyEnabled true` tested on device.
- [ ] **Pre-Launch Report Clean**: Firebase Test Lab automated scan shows zero startup crashes or ANRs.
- [ ] **Permission Denials Handled**: Graceful degradation verified when Android permissions are revoked.

---

# 4. Official Sources

- Android Debug Bridge (`adb`) Reference: https://developer.android.com/tools/adb
- Firebase Test Lab Documentation: https://firebase.google.com/docs/test-lab

---

**Last verified:** August 14, 2026

---

# Related documentation

### Testing

- `testing/README.md`
- `testing/accessibility.md`
- `testing/deep-links.md`
- `testing/device-matrix.md`
- `testing/device-testing.md`
- `testing/e2e.md`
- `testing/integration.md`
- `testing/ios.md`
- `testing/localization.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/payments.md`
- `testing/performance.md`
- `testing/push-notifications.md`
- `testing/release-smoke-tests.md`
- `testing/unit.md`
- `testing/upgrade-migrations.md`

### Pre-release

- `pre-release/README.md`
- `pre-release/final-release-checklist.md`

### Checklists

- `checklists/pre-release.md`

### Troubleshooting

- `troubleshooting/README.md`

### Release strategy

- `release-strategy/beta-testing.md`
