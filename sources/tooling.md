# Mobile Release Engineering Tooling Registry

This document details the primary documentation portals, monitoring platforms (Sentry, Firebase Crashlytics), testing clouds (Firebase Test Lab), commerce engines (RevenueCat), analytics tools (Segment, Mixpanel, Amplitude), attribution partners (AppsFlyer, Branch), and CI/CD services (GitHub Actions) for **Mobile Release Engineering Tooling**.

Engineered in alignment with **2026 platform specifications**, it provides an authoritative citation registry for third-party mobile development and release infrastructure.

This guide is **not**:

- an un-verified vendor advertisement directory
- an authorization mechanism to embed production API tokens in client bundles
- a substitute for inspecting active third-party SDK documentation

---

# 1. Primary Release Engineering Tooling Portals

| Tooling Platform | Primary Official URL | Key Scope & Governance Rules |
|---|---|---|
| **Sentry React Native** | `https://docs.sentry.io/platforms/react-native/` | Production crash reporting, `.dSYM` upload, Hermes source maps. |
| **Firebase Crashlytics** | `https://firebase.google.com/docs/crashlytics` | Real-time crash monitoring, Android ProGuard mapping upload. |
| **Firebase Test Lab** | `https://firebase.google.com/docs/test-lab` | Cloud physical device matrix testing, Play Pre-Launch Reports. |
| **RevenueCat Docs** | `https://www.revenuecat.com/docs/` | StoreKit 2 & Play Billing abstraction, entitlement management. |
| **AppsFlyer Docs** | `https://dev.appsflyer.com/` | Mobile Measurement Partner (MMP), SKAdNetwork 4.0/5.0, ATT timing. |
| **Branch.io Docs** | `https://help.branch.io/` | Deep linking, deferred deep links, attribution measurement. |
| **GitHub Actions** | `https://docs.github.com/en/actions` | CI/CD automation workflows, secret vaults, matrix runners. |
| **Mixpanel Docs** | `https://docs.mixpanel.com/` | User interaction telemetry, retention funnel analytics. |

---

# 2. Key Tooling Integrations Citation Index

```text
┌────────────────────────────────────────────────────────┐
│             RELEASE ENGINEERING TOOLING INDEX          │
│                                                        │
│  - Crash Diagnostics: Sentry & Firebase Crashlytics   │
│  - Cloud QA Testing: Firebase Test Lab                 │
│  - App Commerce: RevenueCat (StoreKit 2 & Play Billing)│
│  - Attribution & Deep Linking: AppsFlyer & Branch.io   │
│  - CI/CD Automation: GitHub Actions & EAS              │
└────────────────────────────────────────────────────────┘
```

---

# 3. Operational Verification Checklist

- [ ] **All URLs Active**: Primary third-party tooling URLs verified returning HTTP 200 OK.
- [ ] **Sentry / Crashlytics Docs Linked**: Symbolication and debug symbol upload docs cited.
- [ ] **RevenueCat Docs Linked**: StoreKit 2 and Play Billing 8/9+ integration docs cited.
- [ ] **MMP Attribution Docs Linked**: SKAN 4.0/5.0 and ATT framework timing docs cited.

---

# 4. Related Documentation

- [Fastlane Documentation Registry](fastlane.md) - Fastlane sources.
- [Expo Documentation Registry](expo.md) - Expo sources.
- [Integrations Subsystem](../integrations/README.md) - Integration handbooks.

---

# 5. Official Sources

- Sentry React Native Docs: https://docs.sentry.io/platforms/react-native/
- Firebase Developer Docs: https://firebase.google.com/docs
- RevenueCat Documentation: https://www.revenuecat.com/docs/

---

**Last verified:** August 14, 2026
