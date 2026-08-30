# EAS Build Pipeline & Cloud Runner Handbook

This handbook details EAS Build profiles (`eas.json`), iOS/Android cloud build runners, custom build credentials, environment variable injection, and auto-incrementing build numbers for **EAS Build** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to execute production cloud builds via EAS.

This guide is **not**:

- an authorization mechanism to compile debug builds for production submission
- a substitute for configuring custom native credentials
- a guide to ignoring build timeouts

---

# 1. EAS Build Execution Architecture

EAS Build provisions cloud macOS and Linux runners to compile clean native binaries from Expo source code.

```text
┌────────────────────────────────────────────────────────┐
│             EAS BUILD EXECUTION PIPELINE               │
│                                                        │
│  - Command: `eas build --platform all --profile prod`  │
│  - iOS Cloud Runner: macOS Xcode 16 Environment       │
│  - Android Cloud Runner: Ubuntu Java 17 / SDK 36 Environment│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Returns Downloadable `.ipa` (iOS) & `.aab` (Android) Artifacts ]
```

---

# 2. Production Profile Configuration Example (`eas.json`)

```json
{
  "build": {
    "production": {
      "channel": "production",
      "autoIncrement": true,
      "env": {
        "APP_ENV": "production"
      },
      "ios": {
        "simulator": false,
        "image": "latest"
      },
      "android": {
        "buildType": "app-bundle"
      }
    }
  }
}
```

---

# 3. Operational Verification Checklist

- [ ] **Xcode Runner Up to Date**: EAS iOS runner image configured with Xcode 16+.
- [ ] **Android App Bundle Set**: `"buildType": "app-bundle"` configured for Android.
- [ ] **Auto-Increment Active**: `"autoIncrement": true` automatically bumps build numbers.

---

# 4. Official Sources

- Expo EAS Build Documentation: https://docs.expo.dev/build/introduction/

---

**Last verified:** August 14, 2026

---

# Related documentation

### EAS

- `release-engineering/eas/README.md`
- `release-engineering/eas/eas-submit.md`
- `release-engineering/eas/eas-update.md`

### Expo

- `frameworks/expo/eas.md`
- `frameworks/expo/build.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/environment-management.md`
