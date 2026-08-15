# Mobile Quality Assurance & Testing Architecture Handbook

This directory contains production handbook guides, testing frameworks, automated E2E test suites, accessibility audits, network resilience protocols, and release smoke test procedures for **Mobile Quality Assurance** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**—specifically Jest 29+, React Native Testing Library (RNTL), Maestro/Detox E2E automation, StoreKit 2 Sandbox, and Firebase Test Lab matrices—it details how to systematically test mobile apps to eliminate crashes and pass store review.

This guide is **not**:

- an authorization mechanism to skip E2E UI testing on physical devices
- a substitute for testing native module bridges
- a manual-only testing guide (automated CI test suites are mandatory)

---

# 1. Architecture of Mobile Testing Pyramid

Mobile testing spans unit tests, integration tests, end-to-end automation, on-device physical testing, and automated cloud device lab matrices.

```text
┌────────────────────────────────────────────────────────┐
│             MOBILE TESTING PYRAMID ARCHITECTURE        │
│                                                        │
│                  ▲  [ E2E / Maestro / Detox ]          │
│                 ╱ ╲  UI Flow & Device Integration      │
│                ╱   ╲                                   │
│               ╱─────╲  [ Integration Tests / MSW ]     │
│              ╱       ╲  State Management & APIs        │
│             ╱─────────╲                                │
│            ╱           ╲  [ Unit Tests / Jest / RNTL ] │
│           ╱─────────────╲  Pure Logic & React Components│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             CLOUD DEVICE LABS & SMOKE TESTING          │
│  - Firebase Test Lab physical Android device matrix    │
│  - Xcode Simulator & Android Emulator matrix           │
│  - Mandatory Release Candidate Smoke Test Suite        │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Testing Handbook | Primary Testing Domain Scope | Key Frameworks & Tools |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, testing pyramid architecture, and QA rules. | High-level testing flow and testing governance. |
| **[unit.md](unit.md)** | Unit testing pure functions, custom hooks, and React components. | Jest 29+, React Native Testing Library (RNTL). |
| **[integration.md](integration.md)** | Integration testing state management, API calls, and navigation. | Mock Service Worker (MSW v2), React Navigation Mocks. |
| **[e2e.md](e2e.md)** | End-to-End automated UI testing across complete app flows. | Maestro, Detox, Appium, GitHub Actions CI runners. |
| **[ios.md](ios.md)** | iOS-specific testing, Xcode UI Tests, and TestFlight validation. | Xcode Simulator CLI (`simctl`), TestFlight Sandbox. |
| **[android.md](android.md)** | Android-specific testing, R8 obfuscation, and Target API 36. | Android Emulator CLI (`adb`), Firebase Test Lab. |
| **[device-matrix.md](device-matrix.md)** | Physical device matrix planning across resolutions & OS versions. | Device coverage matrix, screen densities, RAM limits. |
| **[device-testing.md](device-testing.md)** | On-device physical QA protocol for hardware features. | Physical device deployment, Camera, Biometrics, Bluetooth. |
| **[accessibility.md](accessibility.md)** | Mobile accessibility QA, screen readers, and dynamic font scaling. | VoiceOver, TalkBack, `accessibilityLabel`, Contrast checks. |
| **[localization.md](localization.md)** | Localization testing, RTL layouts, and currency/date formatting. | `I18nManager.forceRTL`, pseudo-localization, `Intl`. |
| **[deep-links.md](deep-links.md)** | Deep links, Universal Links, and Android App Links validation. | `xcrun simctl openurl`, `adb shell am start`, AASA files. |
| **[push-notifications.md](push-notifications.md)** | Push notification testing, background handlers, and APNs/FCM. | FCM HTTP v1 API, APNs `.p8` token testing, Expo Push. |
| **[payments.md](payments.md)** | In-App Purchase & subscription testing in sandbox environments. | StoreKit 2 Sandbox, `.storekit` config, Play Billing 8/9+. |
| **[network.md](network.md)** | Network resilience testing, API latency, and status code boundaries. | Proxyman, Charles Proxy, HTTP error boundaries. |
| **[offline.md](offline.md)** | Offline support, NetInfo state listeners, and queue persistence. | `@react-native-community/netinfo`, offline queue. |
| **[performance.md](performance.md)** | Performance profiling, Hermes memory usage, FPS, and TTI metrics. | Hermes Profiler, Flipper, React Native Perf Monitor. |
| **[release-smoke-tests.md](release-smoke-tests.md)** | Release candidate 10-minute mandatory smoke test suite. | Release Candidate QA checklist, critical pass gates. |
| **[upgrade-migrations.md](upgrade-migrations.md)** | App upgrade testing, SQLite/AsyncStorage schema migrations. | Over-the-install data persistence, state hydration. |

---

# 3. Universal Mobile QA Governance Rules

All testing implementations in this playbook must adhere to five mandatory QA rules:

```text
1. 100% Core Flow Automation Rule
   Critical user paths (Login, Signup, Primary Core Feature, Checkout/Payment) MUST be covered
   by automated E2E tests (Maestro / Detox) executing in CI before every release candidate build.

2. Physical Device Testing Mandate
   App builds MUST be tested on physical iOS and Android hardware before production release.
   Simulator-only testing IS FORBIDDEN for release candidate validation.

3. R8 / ProGuard Minification QA
   Android release builds compiled with R8 minification (`minifyEnabled true`) MUST be tested to
   ensure native bridge symbols and Hermes reflection classes are not stripped by obfuscation.

4. StoreKit 2 & Play Billing Sandbox Verification
   All in-app purchases and subscription paywalls MUST be tested against StoreKit 2 Sandbox and
   Google Play License Testing endpoints before store submission.

5. Zero P0/P1 Release Blockers
   A release candidate binary MUST NOT be submitted to App Review or Google Play Review if any
   P0 (Critical Blocker) or P1 (High Severity) bug remains unresolved in QA testing.
```

---

# 4. Related Repository Documentation

- [Release Checklist Template](../templates/release-checklist.md) - Release checklist.
- [CI/CD Pipelines](../release-engineering/README.md) - Automated CI testing.
- [AI QA Agent](../ai/agents/qa-agent.md) - Automated AI testing.

---

# 5. Official Sources

- React Native Testing Documentation: https://reactnative.dev/docs/testing-overview
- Maestro E2E Framework Docs: https://maestro.mobile.dev/
- Detox E2E Framework Docs: https://wix.github.io/Detox/

---

**Last verified:** August 14, 2026
