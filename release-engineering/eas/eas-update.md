# EAS Update Over-The-Air (OTA) JS Patching Handbook

This handbook details EAS Update Over-The-Air (OTA) JavaScript patching (`eas update`), production channel mapping, `runtimeVersion` policy rules, and OTA rollback procedures for **EAS Update** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to deploy emergency JS hotfixes safely without triggering App Store Review.

This guide is **not**:

- an authorization mechanism to push native code changes via OTA (OTA is for JS and asset changes only)
- a substitute for configuring explicit `runtimeVersion` rules
- a guide to bypassing Apple Guideline 2.5.2 (dynamic code loading rules)

---

# 1. EAS Update OTA Patching Architecture

EAS Update serves updated JavaScript bundles and assets directly to installed mobile app binaries that share a matching `runtimeVersion`.

```text
┌────────────────────────────────────────────────────────┐
│             EAS UPDATE OTA PIPELINE                    │
│                                                        │
│  - Command: `eas update --channel production --message`│
│  - Bundles JS & Assets ──→ Uploads to Expo CDN         │
│  - Installed App Checks CDN on Cold Launch             │
│  - Downloads Update if `runtimeVersion` Matches Binary │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Applied Seamlessly on Next App Cold Launch ]
```

---

# 2. Key `runtimeVersion` Policy Rules

Native native module changes (e.g. adding a new native Bluetooth library) modify the app binary interface, requiring a bump to `runtimeVersion` in `app.json`:

```json
{
  "expo": {
    "runtimeVersion": {
      "policy": "appVersion"
    }
  }
}
```

---

# 3. Operational Verification Checklist

- [ ] **`runtimeVersion` Configured**: `runtimeVersion` policy configured in `app.json`.
- [ ] **JS-Only Scope Verified**: OTA update verified free of native C++/Java/ObjC changes.
- [ ] **Channel Mapped**: `eas update` targeted to correct channel (`production`).

---

# 4. Official Sources

- Expo EAS Update Documentation: https://docs.expo.dev/eas-update/introduction/

---

**Last verified:** August 14, 2026
