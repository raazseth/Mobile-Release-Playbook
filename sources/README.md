# Official Source Registry & Verification Governance

This directory contains official documentation registries, source link indexes, platform policy citations, API deprecation tracking, and verification governance rules for the **Mobile Release Playbook**.

Engineered in alignment with **2026 platform specifications**, it provides an exhaustive, authoritative index of primary documentation sources across Apple Developer, Google Play Console, Expo Framework, React Native Core, Fastlane Automation, and third-party mobile release engineering tooling.

This directory is **not**:

- an un-verified secondary blog aggregator
- an authorization mechanism to cite outdated platform policies
- a substitute for performing live web verification of active store guidelines

---

# 1. Architecture of Source Verification & Governance

To maintain handbook-grade technical integrity, every policy claim, character limit, API parameter, and security rule in this playbook MUST trace back to an authoritative primary source documentation portal.

```text
┌────────────────────────────────────────────────────────┐
│             AUTHORITATIVE PRIMARY SOURCES              │
│                                                        │
│  - Apple Developer Portal (`developer.apple.com`)      │
│  - Google Android & Play Support (`developer.android`) │
│  - Expo Framework Docs (`docs.expo.dev`)               │
│  - React Native Core Docs (`reactnative.dev`)          │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼ (Periodic Source Audit & Verification)
┌────────────────────────────────────────────────────────┐
│             SOURCE VERIFICATION GOVERNANCE             │
│  - Verify URLs return active HTTP 200 OK               │
│  - Audit deprecation cycles (e.g., Target API 36)      │
│  - Update `Last verified:` timestamp footers           │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Mobile Release Playbook Production Handbooks ]
```

---

# 2. Subsystem Directory Taxonomy

| Registry File | Primary Platform / Toolchain Scope | Key Documentation Domains Covered |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, source verification governance, and audit rules. | High-level source architecture and verification framework. |
| **[apple.md](apple.md)** | Apple Developer & App Store Connect Documentation Registry. | App Review Guidelines, StoreKit 2, Privacy Manifests, TestFlight. |
| **[google.md](google.md)** | Google Play Console & Android Developer Documentation Registry. | Play Developer Policies, Target API 36, Play Billing 8.0/9.0+, `.aab`. |
| **[expo.md](expo.md)** | Expo Framework & EAS Build / EAS Submit Documentation Registry. | Expo SDK 51+, EAS Submit, Config Plugins, `expo-updates` OTA. |
| **[react-native.md](react-native.md)** | React Native Core Architecture Documentation Registry. | Hermes Engine, New Architecture (TurboModules/Fabric), Metro. |
| **[fastlane.md](fastlane.md)** | Fastlane Automation Tooling Documentation Registry. | Fastlane Match, Deliver, Supply, Snapshot, Pilot, Frameit. |
| **[tooling.md](tooling.md)** | Third-Party Mobile Release Engineering Tooling Registry. | Sentry, Crashlytics, Firebase Test Lab, RevenueCat, AppsFlyer. |

---

# 3. Universal Source Governance Rules

All documentation and handbook writing in this playbook must adhere to five mandatory source governance rules:

```text
1. Primary Source Mandate
   Every platform policy, API signature, metadata limit, and store guideline MUST cite an official
   primary documentation URL from Apple, Google, Expo, or React Native. Secondary blog tutorials
   MUST NOT be used as primary citations.

2. Deprecation Tracking & Audit
   Deprecated APIs and legacy practices (e.g., Xcode `altool`, legacy FCM HTTP API, Play Billing 7)
   MUST be explicitly flagged as deprecated, replaced with their modern 2026 standards.

3. Live URL Validity Verification
   All external URLs included in `Official Sources` sections MUST return active HTTP 200 OK response
   codes without broken redirects or 404 Not Found errors.

4. Explicit Timestamp Footers
   All handbook files MUST conclude with an explicit `Last verified: Month DD, YYYY` footer
   indicating the most recent date of empirical verification against live platform sources.

5. Machine-Readable Schema Alignment
   YAML and JSON schemas in handbooks MUST match official API specifications (e.g., `eas.json`,
   `PrivacyInfo.xcprivacy`, `apple-app-site-association`, `assetlinks.json`).
```

---

# 4. Related Repository Documentation

- [CONTRIBUTING.md](../CONTRIBUTING.md) - Contribution guidelines.
- [ARCHITECTURE.md](../ARCHITECTURE.md) - Playbook architecture.
- [CHANGELOG.md](../CHANGELOG.md) - Repository changelog.

---

# 5. Official Source Portals

- Apple Developer Documentation: https://developer.apple.com/documentation/
- Android Developer Documentation: https://developer.android.com/docs
- Expo Documentation: https://docs.expo.dev/
- React Native Documentation: https://reactnative.dev/docs/getting-started

---

**Last verified:** August 14, 2026
