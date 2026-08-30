# Release Foundations & Core Concepts Subsystem Handbook

This directory contains production handbook guides, release lifecycle definitions, project configuration patterns, versioning rules, and environment management architectures for **Release Foundations** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details the core technical concepts underpinning mobile release engineering.

This guide is **not**:

- an authorization mechanism to ignore core mobile architecture fundamentals
- a substitute for establishing clean release environments
- a guide to misconfiguring application bundle identifiers

---

# 1. Architecture of Mobile Release Foundations

Release Foundations establish the core technical building blocks required for scalable mobile application deployment.

```text
┌────────────────────────────────────────────────────────┐
│             RELEASE FOUNDATIONS ARCHITECTURE           │
│                                                        │
│  - [mobile-release-lifecycle.md](mobile-release-lifecycle.md)│
│    (End-to-end mobile release lifecycle from code to store)│
│                                                        │
│  - [project-configuration.md](project-configuration.md)│
│    (Expo `app.json`, native Xcode & Gradle configurations)│
│                                                        │
│  - [versioning.md](versioning.md) & [identifiers.md](identifiers.md)│
│    (SemVer 2.0.0 rules, bundle IDs, & package names)  │
│                                                        │
│  - [release-environments.md](release-environments.md)  │
│    (Dev, Staging, & Production environment isolation)  │
│                                                        │
│  - [dependency-management.md](dependency-management.md)│
│    (Lockfile pinning, native module sync, Podfile)    │
│                                                        │
│  - [device-support.md](device-support.md)              │
│    (iOS/Android OS version support & display classes)  │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Foundations Handbook | Core Technical Scope | Key Architecture Concepts |
|---|---|---|
| **[README.md](README.md)** | Subsystem parent index and foundations architecture. | Foundations overview. |
| **[mobile-release-lifecycle.md](mobile-release-lifecycle.md)** | End-to-end mobile release lifecycle and stage gates. | Development → Audit → Build → Release → Telemetry. |
| **[project-configuration.md](project-configuration.md)** | Master project configuration (`app.json`, `build.gradle`, `Info.plist`). | Expo config, native targets, build flavors. |
| **[versioning.md](versioning.md)** | Mobile versioning schemes and SemVer 2.0.0 synchronization. | `versionName`, `versionCode`, `CFBundleVersion`. |
| **[identifiers.md](identifiers.md)** | Bundle Identifiers, Package Names, and App IDs. | Reverse-DNS format, App ID registration. |
| **[release-environments.md](release-environments.md)** | Multi-environment isolation (Dev, Staging, Production). | `.env` scoping, API endpoint isolation. |
| **[dependency-management.md](dependency-management.md)** | Dependency lockfile pinning and native module compatibility. | `package-lock.json`, `Podfile.lock`, `npm audit`. |
| **[device-support.md](device-support.md)** | OS version support policy, min SDKs, and display classes. | Android 16 API 36, iOS 18, 6.9" Retina XDR. |

---

# 3. Operational Verification Checklist

- [ ] **Lifecycle Stage Defined**: App release mapped to clear lifecycle stage.
- [ ] **Config Audited**: `app.json` and native configs verified clean.
- [ ] **Identifiers Verified**: Bundle IDs and package names follow reverse-DNS format.

---

# 4. Official Sources

- React Native Core Concepts: https://reactnative.dev/docs/getting-started
- Expo Fundamentals: https://docs.expo.dev/archive/classic-home/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Foundations

- `foundations/dependency-management.md`
- `foundations/device-support.md`
- `foundations/identifiers.md`
- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/versioning.md`

### Pre-release

- `pre-release/README.md`
- `pre-release/release-readiness.md`

### Release engineering

- `release-engineering/README.md`
- `release-engineering/release-configurations.md`

### Testing

- `testing/README.md`

### Checklists

- `checklists/first-release.md`
