# Version Bumping Automation Subsystem Handbook

This directory contains production handbook guides, CLI scripts, and automated workflows for **Version Bumping** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to synchronize version numbers across JavaScript and native project files automatically.

This guide is **not**:

- an authorization mechanism to manually edit version strings across native files
- a substitute for SemVer 2.0.0 versioning standards
- a guide to out-of-sync iOS `CFBundleVersion` and Android `versionCode`

---

# 1. Version Bumping Architecture & File Sync Flow

Version bumping synchronizes version strings (`1.2.0`) and build numbers (`42`) simultaneously across all cross-platform and native configuration files.

```text
┌────────────────────────────────────────────────────────┐
│             VERSION BUMP SYNCHRONIZATION FLOW          │
│                                                        │
│  [ CLI Command: `npm run version:bump -- --type minor` ]│
│                         │                              │
│                         ▼                              │
│  - `package.json`         ──→ `"version": "1.2.0"`     │
│  - `app.json`             ──→ `"version": "1.2.0"`     │
│  - `ios/MobileApp/Info.plist` ──→ `CFBundleShortVersionString`│
│  - `android/app/build.gradle` ──→ `versionName` & `versionCode`│
│  - `CHANGELOG.md`         ──→ Release Date Entry      │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Automation Handbooks

- **[version-bump.md](version-bump.md)**: Automated version bumping script guide & code implementation.

---

# 3. Operational Verification Checklist

- [ ] **SemVer Standard Met**: Version number follows `MAJOR.MINOR.PATCH` format.
- [ ] **Build Number Incremented**: Android `versionCode` and iOS `CFBundleVersion` incremented monotonically.
- [ ] **Files In Sync**: All 5 target files contain matching version strings.

---

# 4. Official Sources

- Semantic Versioning 2.0.0: https://semver.org/

---

**Last verified:** August 14, 2026
