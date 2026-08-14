# EAS Submit Store Upload Automation Handbook

This handbook details EAS Submit store upload automation (`eas submit`), App Store Connect `.p8` key integration, Google Play GCP Service Account setup, and submit profiles for **EAS Submit** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to automate binary uploads to store platforms.

This guide is **not**:

- an authorization mechanism to upload builds without configuring store credentials in EAS
- a substitute for verifying store metadata prior to submission
- a guide to using interactive upload prompts in CI runners

---

# 1. EAS Submit Upload Architecture

EAS Submit automatically transfers compiled `.ipa` and `.aab` binaries from EAS Build cloud storage directly to App Store Connect and Google Play Console.

```text
┌────────────────────────────────────────────────────────┐
│             EAS SUBMIT AUTOMATION PIPELINE             │
│                                                        │
│  - Command: `eas submit --platform all --latest`       │
│  - iOS: Transfers `.ipa` to App Store Connect          │
│    (Authenticated via App Store Connect API Key .p8)   │
│  - Android: Transfers `.aab` to Play Internal Track    │
│    (Authenticated via GCP Service Account JSON)        │
└────────────────────────────────────────────────────────┘
```

---

# 2. Submit Profile Configuration Example (`eas.json`)

```json
{
  "submit": {
    "production": {
      "ios": {
        "appleId": "developer@example.com",
        "ascApiKeyPath": "./asc-api-key.p8",
        "ascApiKeyId": "2X997B2844",
        "ascApiKeyIssuerId": "69a6de70-0000-0000-0000-000000000000"
      },
      "android": {
        "serviceAccountKeyPath": "./google-service-account.json",
        "track": "internal"
      }
    }
  }
}
```

---

# 3. Operational Verification Checklist

- [ ] **EAS Submit Profile Configured**: `eas.json` contains `submit.production` profile.
- [ ] **`.p8` Key Injected**: App Store Connect API key configured for iOS submission.
- [ ] **Service Account Injected**: Google Play Service Account JSON configured for Android submission.

---

# 4. Official Sources

- Expo EAS Submit Documentation: https://docs.expo.dev/submit/introduction/

---

**Last verified:** August 14, 2026
