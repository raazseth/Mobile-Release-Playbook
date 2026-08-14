# Google Play Developer API Automation Handbook

This handbook details Google Play Developer API v3 automation, GCP Service Account JSON configuration, Fastlane Supply integration, track deployment (Internal, Closed, Production), and release notes sync for **Google Play API** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to automate Google Play Store deployments.

This guide is **not**:

- an authorization mechanism to upload builds without configuring Service Account permissions in Play Console
- a substitute for securing GCP Service Account JSON keys
- a guide to using legacy Play Publishing API v2 endpoints (deprecated)

---

# 1. Google Play Developer API Authentication Architecture

The Google Play Developer API v3 uses Google Cloud OAuth2 Service Account JSON keys to authenticate automated deployment tools.

```text
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY DEVELOPER API FLOW             │
│                                                        │
│  - Input: GCP Service Account JSON Key (`pc-api-key.json`)│
│  - Play Console: Grant Service Account 'Release Manager'│
│  - Fastlane Supply / EAS Submit OAuth2 Exchange        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Automated `.aab` Upload & Track Promotion via REST API v3 ]
```

---

# 2. Fastlane Supply Configuration Example (`Fastfile`)

Automate Google Play Internal testing track deployment using Fastlane Supply:

```ruby
lane :android_internal do
  gradle(task: "bundle", build_type: "Release")

  upload_to_play_store(
    track: "internal",
    aab: "android/app/build/outputs/bundle/release/app-release.aab",
    json_key_data: ENV["PLAY_STORE_JSON_KEY_DATA"]
  )
end
```

---

# 3. Operational Verification Checklist

- [ ] **GCP Service Account Authorized**: Service Account granted Release Manager permission in Play Console.
- [ ] **JSON Key Injected**: JSON key injected as base64 string in CI secret environment.
- [ ] **API v3 Endpoint Active**: Fastlane Supply communicates with Google Play API v3 endpoint.

---

# 4. Official Sources

- Google Play Developer API v3 Reference: https://developers.google.com/android-publisher

---

**Last verified:** August 14, 2026
