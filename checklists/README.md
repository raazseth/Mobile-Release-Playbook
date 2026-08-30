# Operational Release Checklists Subsystem Handbook

This directory contains production-ready verification checklists, pre-flight inspection forms, GO/NO-GO readiness gates, and post-release verification procedures for **Release Checklists** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides actionable, step-by-step audit checklists for release engineering teams.

This guide is **not**:

- an authorization mechanism to skip pre-release audit quality gates
- a substitute for executing automated CI/CD unit and integration tests
- a guide to signing off on releases with open P0 or P1 bugs

---

# 1. Architecture of Operational Release Checklists

Checklists enforce strict operational rigor across every stage of the mobile application release lifecycle.

```text
┌────────────────────────────────────────────────────────┐
│             RELEASE CHECKLIST ARCHITECTURE             │
│                                                        │
│  - [first-release.md](first-release.md)               │
│    (Initial store submission audit for new apps)       │
│                                                        │
│  - [pre-release.md](pre-release.md) & [production.md](production.md)│
│    (Pre-flight quality gates & store deployment)       │
│                                                        │
│  - [ios.md](ios.md) & [android.md](android.md)         │
│    (Platform-specific Xcode & Play Console checks)     │
│                                                        │
│  - [cross-platform.md](cross-platform.md) & [post-release.md](post-release.md)│
│    (Multi-platform coordination & rollout monitoring)  │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Checklist Handbook | Primary Audit Scope | Key Inspection Gates |
|---|---|---|
| **[README.md](README.md)** | Subsystem parent index and checklist architecture. | Operational audit overview. |
| **[first-release.md](first-release.md)** | First-time store submission audit checklist for new apps. | Account setup, D-U-N-S, IARC, initial submission. |
| **[pre-release.md](pre-release.md)** | Pre-flight release readiness checklist and GO/NO-GO criteria. | Code freeze, security scan, dependency audit. |
| **[production.md](production.md)** | Production release deployment checklist and binary packaging. | Signed `.ipa`/`.aab`, store upload, metadata sync. |
| **[ios.md](ios.md)** | iOS-specific release checklist for App Store Connect. | Privacy Manifest, 6.9" screenshots, TestFlight. |
| **[android.md](android.md)** | Android-specific release checklist for Google Play Console. | Target SDK 36, Data Safety, Staged Rollout. |
| **[cross-platform.md](cross-platform.md)** | Multi-platform synchronization checklist (EAS / Fastlane). | Version string sync, icon assets, release notes. |
| **[post-release.md](post-release.md)** | Post-release monitoring checklist and progressive rollout gates. | Sentry crash rate, ANR tracking, store reviews. |

---

# 3. Operational Verification Checklist

- [ ] **Checklist Selected**: Appropriate checklist chosen for target release stage.
- [ ] **100% Sign-Off**: All mandatory inspection items verified and signed off by lead engineer.
- [ ] **Zero Blockers**: Zero open P0 or P1 bugs present in release candidate binary.

---

# 4. Official Sources

- Pre-Release Readiness Handbook: [../pre-release/release-readiness.md](../pre-release/release-readiness.md)

---

**Last verified:** August 14, 2026

---

# Related documentation

### Checklists

- `checklists/android.md`
- `checklists/cross-platform.md`
- `checklists/first-release.md`
- `checklists/ios.md`
- `checklists/post-release.md`
- `checklists/pre-release.md`
- `checklists/production.md`

### Pre-release

- `pre-release/final-release-checklist.md`
- `pre-release/release-readiness.md`

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Store operations

- `store-operations/README.md`

### Templates

- `templates/release-checklist.md`
