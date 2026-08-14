# Transporter & Store Upload Failures Triage

This document details diagnostic workflows, `ITMS-90032` (alpha channel transparency), `ITMS-90034` (duplicate build number), and Google Play Service Account authentication errors for **Store Upload Failures** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for resolving store upload failures.

This guide is **not**:

- an authorization mechanism to upload builds with duplicate build numbers
- a substitute for stripping alpha transparency channels from PNG assets
- a guide to using deprecated `altool` upload commands

---

# 1. Store Upload Failure Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             STORE UPLOAD FAILURE DIAGNOSTIC            │
│                                                        │
│  - Inspect Transporter / Fastlane / EAS Submit logs   │
│  - Extract exact ITMS error code or Play Console error │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ `ITMS-90032` Alpha Channel ]      [ `ITMS-90034` Duplicate Build ]
Strip alpha from PNG assets via     Increment `buildNumber` in `app.json`
`magick mogrify -alpha off *.png`   & re-compile build
```

---

# 2. Key Store Upload Error Scenarios & Resolutions

### Error 1: Apple `ITMS-90032: Invalid Image Path - No alpha channel allowed`
- **Symptom**: App Store Connect rejects screenshot or app icon upload.
- **Root Cause**: PNG file contains an active alpha transparency channel.
- **Resolution**: Strip alpha channel using ImageMagick CLI:
  ```bash
  magick mogrify -alpha off ./screenshots/*.png
  ```

### Error 2: Apple `ITMS-90034: Redundant Build Version`
- **Symptom**: Upload rejected because build number `1.2.0.1` was already uploaded.
- **Resolution**: Increment `buildNumber` in `app.json` to `1.2.0.2` and re-compile.

---

# 3. Operational Verification Checklist

- [ ] **Alpha Channels Stripped**: Screenshots and icons verified free of alpha transparency (`hasAlpha: NO`).
- [ ] **Build Number Incremented**: `buildNumber` / `versionCode` strictly higher than previous uploads.
- [ ] **Service Account Authorized**: GCP Service Account JSON verified active in Play Console.

---

# 4. Official Sources

- Transporter Upload Help: https://developer.apple.com/help/app-store-connect/#/devb1c6762e2

---

**Last verified:** August 14, 2026
