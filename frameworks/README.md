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
│    Expo SDK 51+, EAS Build, Config Plugins, Prebuild,  │
│    Expo Modules API, and OTA updates.                  │
│                                                        │
│  - [react-native/](react-native/README.md)            │
│    React Native Core, New Architecture (Fabric/Turbo), │
│    Hermes JS engine, CocoaPods, and Gradle native tool.│
│                                                        │
│  - [community/](community/README.md)                  │
│    Third-party community module evaluation, license    │
│    compliance, native autolinking, and native patches. │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Framework Subsystem | Primary Scope & Architecture | Key Guides & Tools |
|---|---|---|
| **[README.md](README.md)** | Subsystem parent index and framework architecture. | High-level framework overview. |
| **[expo/](expo/README.md)** | Expo SDK, Config Plugins, EAS tooling, Prebuild workflows. | [config-plugins.md](expo/config-plugins.md), [eas-build.md](expo/eas-build.md), [prebuild.md](expo/prebuild.md). |
| **[react-native/](react-native/README.md)** | React Native Core, New Architecture, Hermes, CocoaPods, Gradle. | [new-architecture.md](react-native/new-architecture.md), [hermes.md](react-native/hermes.md), [cocoapods.md](react-native/cocoapods.md). |
| **[community/](community/README.md)** | Third-party community modules, autolinking, patch-package. | [module-evaluation.md](community/module-evaluation.md), [autolinking.md](community/autolinking.md). |

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
