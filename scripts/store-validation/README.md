# Store API Submission Validation Subsystem Handbook

This directory contains production handbook guides, CLI scripts, and API connection verification tools for **Store Validation** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to verify App Store Connect API keys and Google Play Service Account JSON connectivity before launching deployment tasks.

This guide is **not**:

- an authorization mechanism to test store API keys during production deployment steps
- a substitute for verifying `.p8` API key permissions in App Store Connect
- a guide to ignoring API authentication errors

---

# 1. Store Validation Architecture

Store validation executes pre-flight HTTP handshake calls to App Store Connect REST API and Google Play Developer API v3 endpoints to confirm credential validity and role permissions.

```text
┌────────────────────────────────────────────────────────┐
│             STORE API VALIDATION HANDSHAKE             │
│                                                        │
│  - App Store Connect API (`.p8` Auth Key)              │
│    ──→ GET `https://api.appstoreconnect.apple.com/v1/apps`│
│                                                        │
│  - Google Play Developer API (Service Account JSON)   │
│    ──→ GET Google Play Publisher Edits Endpoint        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified HTTP 200 Handshake ──→ Store Credentials Active ]
```

---

# 2. Key Automation Handbooks

- **[validate-store.md](validate-store.md)**: Automated store API connection validation script guide & implementation.

---

# 3. Operational Verification Checklist

- [ ] **App Store Connect Handshake**: HTTP 200 status returned from App Store Connect API.
- [ ] **Google Play API Handshake**: OAuth2 token exchanged successfully with Google Cloud.
- [ ] **Credentials Valid**: Key IDs and Issuer IDs authenticated cleanly.

---

# 4. Official Sources

- App Store Connect API Documentation: https://developer.apple.com/documentation/appstoreconnectapi/
- Google Play Developer API Documentation: https://developers.google.com/android-publisher

---

**Last verified:** August 14, 2026

---

# Related documentation

### Store validation scripts

- `scripts/store-validation/validate-store.md`

### Store operations

- `store-operations/app-listing.md`

### Pre-release

- `pre-release/metadata-audit.md`
