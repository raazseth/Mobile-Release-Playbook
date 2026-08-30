# Expo Framework & EAS Documentation Registry

This document details the primary documentation portals, Expo SDK specifications, EAS Build, EAS Submit, Expo Config Plugins, Over-The-Air (OTA) updates (`expo-updates`), and native module APIs for **Expo & React Native Development**.

Engineered in alignment with **2026 platform specifications**, it provides an authoritative citation registry for Expo framework development and release automation.

This guide is **not**:

- an un-verified secondary tutorial list
- an authorization mechanism to cite deprecated Expo CLI tools (`expo build:android` / `expo build:ios` are deprecated in favor of EAS)
- a substitute for inspecting active Expo documentation

---

# 1. Primary Expo Documentation Portals

| Documentation Domain | Primary Official URL | Key Scope & Governance Rules |
|---|---|---|
| **Expo Documentation** | `https://docs.expo.dev/` | Core Expo SDK, guides, architecture, and module reference. |
| **EAS Build** | `https://docs.expo.dev/build/introduction/` | Managed cloud & local build service for iOS (`.ipa`) and Android (`.aab`). |
| **EAS Submit** | `https://docs.expo.dev/submit/introduction/` | Automated build submission to App Store Connect & Google Play Console. |
| **Expo Config Plugins** | `https://docs.expo.dev/config-plugins/introduction/` | Prebuild native code modifiers (`app.json` / `app.config.ts`). |
| **`expo-updates` (OTA)** | `https://docs.expo.dev/eas-update/introduction/` | Over-The-Air JavaScript & asset updates pipeline. |
| **Expo App Icons** | `https://docs.expo.dev/guides/app-icons/` | App icon generation, Android Adaptive Icons, and Splash Screens. |
| **Expo Localization** | `https://docs.expo.dev/guides/localization/` | Device locale detection, `i18n`, and RTL layout support. |

---

# 2. Key Expo Configuration Specifications Index

```text
┌────────────────────────────────────────────────────────┐
│             EXPO CONFIGURATION CITATION INDEX          │
│                                                        │
│  - Expo Application Services (EAS):                    │
│    Replaces legacy `expo build` CLI completely.        │
│                                                        │
│  - Config Plugins (`app.json`):                        │
│    Injects native permissions & Plist/Manifest keys.   │
│                                                        │
│  - `expo-updates` (EAS Update):                        │
│    Must conform to Apple Guideline 3.3.2 (No native    │
│    binary updates allowed over the air).               │
└────────────────────────────────────────────────────────┘
```

---

# 3. Operational Verification Checklist

- [ ] **EAS Tooling Cited**: EAS Build and EAS Submit cited as canonical build/submit tools.
- [ ] **Config Plugins Documented**: Prebuild config plugin documentation linked.
- [ ] **EAS Update Guidelines Linked**: `expo-updates` guidelines linked in compliance docs.

---

# 4. Related Documentation

- [React Native Documentation Registry](react-native.md) - React Native sources.
- [Fastlane Documentation Registry](fastlane.md) - Fastlane sources.
- [Cross-Platform Publishing Handbook](../publishing/cross-platform/README.md) - Publishing workflows.

---

# 5. Official Sources

- Expo Documentation Portal: https://docs.expo.dev/

---

**Last verified:** August 14, 2026

