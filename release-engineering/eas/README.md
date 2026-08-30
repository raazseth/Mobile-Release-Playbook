# Expo Application Services (EAS) Subsystem Handbook

This directory contains production handbook guides, `eas.json` profiles, EAS Build cloud runners, EAS Submit store upload automation, and EAS Update Over-The-Air (OTA) patching for **EAS Infrastructure** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to manage Expo cloud build and deployment pipelines.

This guide is **not**:

- an authorization mechanism to deploy un-tested OTA JS updates to production users
- a substitute for configuring custom native build credentials in EAS
- a guide to misconfiguring `runtimeVersion` mapping

---

# 1. Expo Application Services (EAS) Pipeline Architecture

EAS provides a unified cloud platform for compiling native binaries (EAS Build), submitting binaries to app stores (EAS Submit), and publishing Over-The-Air JavaScript updates (EAS Update).

```text
┌────────────────────────────────────────────────────────┐
│             EAS CLOUD PIPELINE ARCHITECTURE            │
│                                                        │
│  - EAS Build: `eas build --platform all` (Cloud Runner)│
│  - EAS Submit: `eas submit --platform all` (Auto Upload)│
│  - EAS Update: `eas update --channel production` (OTA) │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| EAS Guide | Core EAS Scope | Key CLI Commands |
|---|---|---|
| **[README.md](README.md)** | Subsystem index and EAS cloud pipeline architecture. | Subsystem overview. |
| **[eas-build.md](eas-build.md)** | EAS Build profiles (`eas.json`), cloud build runners, credentials. | `eas build --platform ios --profile production`. |
| **[eas-submit.md](eas-submit.md)** | EAS Submit store upload automation for iOS and Android. | `eas submit --platform all --latest`. |
| **[eas-update.md](eas-update.md)** | EAS Update Over-The-Air (OTA) JS patching and runtime versions. | `eas update --channel production`. |

---

# 3. Operational Verification Checklist

- [ ] **EAS CLI Installed**: `eas-cli` version >= 10.0.0 installed in CI environment.
- [ ] **`eas.json` Validated**: `eas.json` contains `development`, `preview`, and `production` profiles.
- [ ] **Credentials Synced**: iOS Apple Distribution Certs & Android Keystores managed in EAS Credentials.

---

# 4. Official Sources

- Expo EAS Documentation: https://docs.expo.dev/eas/

---

**Last verified:** August 14, 2026

---

# Related documentation

### EAS

- `release-engineering/eas/eas-build.md`
- `release-engineering/eas/eas-submit.md`
- `release-engineering/eas/eas-update.md`

### Expo

- `frameworks/expo/eas.md`
- `frameworks/expo/build.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/environment-management.md`
