# React Native Core Architecture Documentation Registry

This document details the primary documentation portals, React Native Core architecture, Hermes JS Engine, New Architecture (TurboModules & Fabric), Metro bundler, ProGuard/R8 rules, and native bridging for **React Native App Development**.

Engineered in alignment with **2026 platform specifications**, it provides an authoritative citation registry for React Native framework architecture and native integration.

This guide is **not**:

- an un-verified secondary tutorial list
- an authorization mechanism to cite legacy bridge architecture without noting the New Architecture
- a substitute for inspecting active React Native documentation

---

# 1. Primary React Native Documentation Portals

| Documentation Domain | Primary Official URL | Key Scope & Governance Rules |
|---|---|---|
| **React Native Core Docs** | `https://reactnative.dev/docs/getting-started` | Core framework documentation, API references, and component guides. |
| **New Architecture Guide** | `https://reactnative.dev/docs/the-new-architecture/landing-page` | TurboModules (native modules) and Fabric (UI renderer) architecture. |
| **Hermes JS Engine** | `https://hermesengine.dev/` | Optimized JavaScript engine for React Native on iOS and Android. |
| **Metro Bundler Docs** | `https://reactnative.dev/docs/metro` | JavaScript bundler, asset resolution, and source maps generation. |
| **React Native Linking** | `https://reactnavigation.org/docs/deep-linking/` | Deep linking, custom URL schemes, Universal Links, and App Links. |
| **ProGuard Rules** | `https://reactnative.dev/docs/signed-apk-android#enabling-proguard-to-reduce-the-size-of-the-apk-optional` | Android code obfuscation and preservation rules for native modules. |

---

# 2. Key Architecture Specifications Index

```text
┌────────────────────────────────────────────────────────┐
│             REACT NATIVE ARCHITECTURE INDEX            │
│                                                        │
│  - Hermes Bytecode Engine:                             │
│    Default JS engine for iOS and Android in 2026.      │
│                                                        │
│  - New Architecture (Fabric & TurboModules):           │
│    Replaces legacy asynchronous bridge with C++ JSI.   │
│                                                        │
│  - Metro Source Maps:                                  │
│    Mandatory for de-obfuscating Hermes JS stack traces.│
└────────────────────────────────────────────────────────┘
```

---

# 3. Operational Verification Checklist

- [ ] **React Native Core URLs Active**: Official `reactnative.dev` URLs verified returning HTTP 200 OK.
- [ ] **Hermes Engine Documented**: Hermes engine bytecode and source map documentation linked.
- [ ] **New Architecture Cited**: Fabric renderer and TurboModules JSI architecture cited.

---

# 4. Related Documentation

- [Expo Documentation Registry](expo.md) - Expo sources.
- [Apple Documentation Registry](apple.md) - iOS sources.
- [Google Documentation Registry](google.md) - Android sources.

---

# 5. Official Sources

- React Native Documentation Portal: https://reactnative.dev/docs/getting-started

---

**Last verified:** August 14, 2026

