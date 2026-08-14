# Fastlane Automation Subsystem Handbook

This directory contains production handbook guides, Fastlane `Fastfile` lane architecture, `Appfile` configuration, `Matchfile` certificate synchronization, and automated store submission lanes for **Fastlane Automation** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to automate mobile build, signing, and store delivery via Fastlane CLI.

This guide is **not**:

- an authorization mechanism to run manual Fastlane actions without automated CI runners
- a substitute for configuring Fastlane Match
- a guide to storing raw API key secrets inside `Fastfile` source code

---

# 1. Fastlane Automation Pipeline Architecture

Fastlane provides an open-source automation toolchain for managing code signing (`match`), building binaries (`gym`), uploading to TestFlight (`pilot`), and deploying to Google Play (`supply`).

```text
┌────────────────────────────────────────────────────────┐
│             FASTLANE AUTOMATION WORKFLOW               │
│                                                        │
│  - `fastlane match appstore`  ──→ Sync Code Signing   │
│  - `fastlane gym`            ──→ Archive `.ipa`       │
│  - `fastlane pilot`          ──→ Upload TestFlight    │
│  - `fastlane supply`         ──→ Upload Play Console  │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Fastlane File | Core Fastlane Scope | Key Actions & Functions |
|---|---|---|
| **[README.md](README.md)** | Subsystem index and Fastlane automation architecture. | Toolchain overview. |
| **[fastfile.md](fastfile.md)** | Fastlane `Fastfile` lane architecture, custom lanes. | `lane :beta`, `lane :release`, gym, pilot, supply. |
| **[appfile.md](appfile.md)** | Fastlane `Appfile` configuration, Bundle ID mapping. | `app_identifier`, `apple_id`, `itc_team_id`. |
| **[matchfile.md](matchfile.md)** | Fastlane `Matchfile` setup, encrypted git / S3 backends. | `git_url`, `storage_mode`, `app_identifier`. |

---

# 3. Operational Verification Checklist

- [ ] **Fastlane CLI Installed**: `fastlane` version >= 2.220.0 installed in CI environment.
- [ ] **`Fastfile` Validated**: `Fastfile` defines clean `:beta` and `:release` lanes.
- [ ] **Secrets Injected**: Passwords and API keys injected via environment variables.

---

# 4. Official Sources

- Fastlane Documentation: https://docs.fastlane.tools/

---

**Last verified:** August 14, 2026
