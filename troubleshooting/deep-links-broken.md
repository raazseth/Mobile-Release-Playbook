# Deep Links & Universal Links Breakdown Triage

This document details diagnostic workflows, AASA file 404 errors, `assetlinks.json` SHA-256 fingerprint mismatches, scheme collision errors, and iOS/Android routing failures for **Deep Links Troubleshooting** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for resolving deep link navigation breakdowns.

This guide is **not**:

- an authorization mechanism to host AASA files without HTTPS
- a substitute for verifying SHA-256 fingerprints in Google Play Console
- a guide to skipping cold start deep link testing

---

# 1. Universal Links / App Links Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             DEEP LINK BREAKDOWN DIAGNOSTIC             │
│                                                        │
│  - Test link open via `simctl` or `adb` CLI             │
│  - Inspect domain association configuration           │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ iOS AASA File 404 / Invalid Header ] [ Android Fingerprint Mismatch ]
Host `apple-app-site-association`   Update SHA-256 fingerprint in
at `https://domain/.well-known/`    `assetlinks.json` from Play Signing
```

---

# 2. Key Deep Link Errors & Resolutions

### Scenario A: iOS Universal Link Opens in Safari Instead of App
- **Root Cause**: Apple CDN failed to fetch or parse `apple-app-site-association` (AASA) file.
- **Resolution**:
  1. Verify AASA file is served over HTTPS at `https://example.com/.well-known/apple-app-site-association`.
  2. Ensure HTTP header `Content-Type` is set to `application/json`.
  3. Validate AASA syntax using Apple's official App Search API Validator.

### Scenario B: Android App Link Opens Browser Disambiguation Dialog
- **Root Cause**: `assetlinks.json` SHA-256 fingerprint does not match Google Play App Signing key fingerprint.
- **Resolution**: Copy App Signing certificate SHA-256 fingerprint from Play Console -> Setup -> App Integrity, and paste into `assetlinks.json`:
  ```json
  [{
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "com.example.mobileapp",
      "sha256_cert_fingerprints": ["14:6D:E8:..."]
    }
  }]
  ```

---

# 3. Operational Verification Checklist

- [ ] **AASA Active**: AASA file hosted at `https://domain/.well-known/apple-app-site-association` with HTTP 200 OK.
- [ ] **Android Fingerprint Matched**: `assetlinks.json` contains SHA-256 fingerprint from Play App Signing.
- [ ] **Cold Start Navigates**: Link trigger opens app from zero state and lands on target route.

---

# 4. Official Sources

- Apple Universal Links Troubleshooting: https://developer.apple.com/documentation/xcode/supporting-universal-links-in-your-app
- Android App Links Verification: https://developer.android.com/training/app-links/verify-site-associations

---

**Last verified:** August 14, 2026
