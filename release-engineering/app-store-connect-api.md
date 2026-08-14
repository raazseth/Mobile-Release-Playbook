# App Store Connect API Automation Handbook

This handbook details App Store Connect API automation, JWT token generation, `.p8` API key authentication, Fastlane Pilot integration, TestFlight build distribution, and metadata sync for **App Store Connect API** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to automate App Store Connect operations via API.

This guide is **not**:

- an authorization mechanism to use 2FA-bound personal Apple ID credentials for CI automation
- a substitute for securing `.p8` API key files
- a guide to hardcoding App Store Connect Issuer IDs in public repositories

---

# 1. App Store Connect API JWT Authentication Flow

The App Store Connect API uses short-lived JSON Web Tokens (JWT) signed with ES256 using an AuthKey `.p8` private key file.

```text
┌────────────────────────────────────────────────────────┐
│             APP STORE CONNECT API JWT FLOW             │
│                                                        │
│  - Input: Issuer ID, Key ID, Private Key (.p8 file)    │
│  - Header: `{ "alg": "ES256", "kid": "KEY_ID" }`       │
│  - Payload: `{ "iss": "ISSUER_ID", "exp": 1723650000 }`│
│  - Sign with Private Key ──→ Bearer JWT Auth Token    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Authenticated API Requests to App Store Connect REST API ]
```

---

# 2. Fastlane Pilot Integration Example (`Fastfile`)

Automate TestFlight build upload and distribution using Fastlane Pilot with `.p8` API keys:

```ruby
lane :beta do
  api_key = app_store_connect_api_key(
    key_id: ENV["APP_STORE_CONNECT_KEY_ID"],
    issuer_id: ENV["APP_STORE_CONNECT_ISSUER_ID"],
    key_content: ENV["APP_STORE_CONNECT_API_KEY_KEY"],
    is_key_content_base64: true
  )

  build_app(workspace: "ios/MobileApp.xcworkspace", scheme: "MobileApp")
  upload_to_testflight(api_key: api_key, skip_waiting_for_build_processing: true)
end
```

---

# 3. Operational Verification Checklist

- [ ] **`.p8` API Key Configured**: Key ID, Issuer ID, and base64 `.p8` key injected in CI.
- [ ] **No 2FA Prompts**: Fastlane Pilot executes in CI without interactive 2FA prompts.
- [ ] **TestFlight Upload Verified**: Build uploads successfully to App Store Connect processing queue.

---

# 4. Official Sources

- Apple App Store Connect API Overview: https://developer.apple.com/documentation/appstoreconnectapi/

---

**Last verified:** August 14, 2026
