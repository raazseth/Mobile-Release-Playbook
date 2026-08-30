# Multi-Environment Release Configurations Handbook

This handbook details multi-environment release configurations (`app.json`, `eas.json`, `build.gradle`, `Info.plist`), iOS Scheme targets, Android build flavors, and bundle identifier isolation for **Release Configurations** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to configure distinct Development, Staging, and Production targets.

This guide is **not**:

- an authorization mechanism to share production bundle identifiers with staging builds
- a substitute for using `eas.json` profiles
- a guide to hardcoding API keys in build configurations

---

# 1. Multi-Environment Configuration Architecture

Environment isolation prevents test data from polluting production databases and allows staging builds to run concurrently on the same device alongside production apps.

```text
┌────────────────────────────────────────────────────────┐
│             MULTI-ENVIRONMENT ISOLATION SCHEMA         │
│                                                        │
│  - Development: `com.example.mobileapp.dev`            │
│  - Staging:     `com.example.mobileapp.staging`        │
│  - Production:  `com.example.mobileapp`                │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Distinct Bundle IDs, Icons, API Endpoints, & Entitlements ]
```

---

# 2. Key `eas.json` Profile Configuration Example

```json
{
  "cli": {
    "version": ">= 10.0.0"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "preview": {
      "distribution": "internal",
      "channel": "staging"
    },
    "production": {
      "distribution": "store",
      "channel": "production",
      "autoIncrement": true
    }
  }
}
```

---

# 3. Operational Verification Checklist

- [ ] **Bundle IDs Isolated**: Development, Staging, and Production use distinct bundle identifiers.
- [ ] **`eas.json` Profiles Configured**: `development`, `preview`, and `production` profiles defined.
- [ ] **Store Distribution Set**: `production` profile configured with `"distribution": "store"`.

---

# 4. Official Sources

- Expo EAS Build Profiles Guide: https://docs.expo.dev/build/eas-json/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Release engineering

- `release-engineering/README.md`
- `release-engineering/app-store-connect-api.md`
- `release-engineering/build-systems.md`
- `release-engineering/environment-management.md`
- `release-engineering/google-play-api.md`
- `release-engineering/release-pipelines.md`

### EAS

- `release-engineering/eas/README.md`

### Fastlane

- `release-engineering/fastlane/README.md`

### GitHub Actions

- `release-engineering/github-actions/README.md`

### Signing

- `signing/README.md`

### AI orchestration

- `ai/orchestration/mcp.md`
- `ai/orchestration/tool-permissions.md`
