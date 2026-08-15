# Mobile Application Frameworks Subsystem Handbook

This directory contains production handbook guides, Expo framework configurations, React Native CLI native toolchains, and community module release rules for **Mobile Frameworks** in mobile applications.

Engineered in alignment with **2026 platform specifications**, it details how to configure Expo, React Native Core, and third-party community modules for production releases.

This guide is **not**:

- an authorization mechanism to use deprecated Expo SDK versions
- a substitute for managing native C++ / ObjC / Swift / Kotlin bridge compatibility
- a guide to un-tested community library integrations

---

# 1. Architecture of Mobile Application Frameworks

Mobile frameworks provide the core JavaScript runtime, native bridge architecture, and UI layout engine for cross-platform applications.

```text
┌────────────────────────────────────────────────────────┐
│             MOBILE FRAMEWORKS ARCHITECTURE             │
│                                                        │
│  - [expo/](expo/README.md)                            │
│    Expo SDK 51+, EAS Build, Config, Credentials,      │
│    Development Builds, Submit, and Updates.            │
│                                                        │
│  - [react-native/](react-native/README.md)            │
│    React Native Core, Android/iOS releases, Hermes,   │
│    Fastlane automation, Signing, & Debugging.          │
│                                                        │
│  - [community/](community/README.md)                  │
│    Third-party community module evaluation, license    │
│    compliance, and framework addition procedures.      │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Framework Subsystem | Primary Scope & Architecture | Key Guides & Tools |
|---|---|---|
| **[README.md](README.md)** | Subsystem parent index and framework architecture. | High-level framework overview. |
| **[expo/](expo/README.md)** | Expo SDK, App Config, EAS Tooling, Credentials, Submissions. | [app-config.md](expo/app-config.md), [build.md](expo/build.md), [eas.md](expo/eas.md), [submit.md](expo/submit.md), [updates.md](expo/updates.md). |
| **[react-native/](react-native/README.md)** | React Native Core, iOS & Android releases, Fastlane, Signing. | [android-release.md](react-native/android-release.md), [ios-release.md](react-native/ios-release.md), [fastlane.md](react-native/fastlane.md), [signing.md](react-native/signing.md). |
| **[community/](community/README.md)** | Third-party community modules & framework integration. | [adding-a-framework.md](community/adding-a-framework.md). |

---

# 3. Operational Verification Checklist

- [ ] **Framework Up to Date**: Active Expo SDK / React Native version verified supported in 2026.
- [ ] **New Architecture Verified**: Fabric renderer and TurboModules verified functional.
- [ ] **Hermes Engine Active**: Hermes engine enabled for iOS and Android release builds.

---

# 4. Official Sources

- Expo Documentation: https://docs.expo.dev/
- React Native Documentation: https://reactnative.dev/

---

**Last verified:** August 14, 2026
