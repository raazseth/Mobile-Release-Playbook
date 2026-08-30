# Android App Links Integration

This guide details the integration architecture, domain verification requirements, `assetlinks.json` hosting specifications, and release testing protocols for **Android App Links** in React Native and Expo applications.

Unlike standard custom scheme deep links (`myapp://`), Android App Links open verified web URLs (`https://example.com/item/123`) directly inside the installed application without displaying a browser disambiguation dialog.

This guide is **not**:

- an iOS Universal Links guide (see [universal-links/](../universal-links/README.md))
- a substitute for hosting `assetlinks.json` over secure HTTPS web endpoints
- a custom URL scheme configuration (see [deep-links/](../deep-links/README.md))

---

# 1. Integration Architecture & Verification Flow

Android App Links utilize domain ownership verification powered by Digital Asset Links:

```text
[ User Clicks HTTPS Link: `https://example.com/item/123` ]
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            ANDROID OS DOMAIN VERIFICATION              │
│  - OS fetches `https://example.com/.well-known/assetlinks.json`
│  - Verifies APK SHA-256 fingerprint matches JSON       │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Domain Verified ]                [ Verification Failed ]
 Open App Directly without           Open Web Browser or
 Disambiguation Dialog               Display App Chooser
```

---

# 2. Digital Asset Links (`assetlinks.json`) Specification

The web domain must host a valid `assetlinks.json` file at the exact path:

```text
https://example.com/.well-known/assetlinks.json
```

### `assetlinks.json` File Content

```json
[
  {
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "com.example.mobileapp",
      "sha256_cert_fingerprints": [
        "FA:C6:17:45:DC:09:03:78:6F:B9:ED:E6:2A:96:2B:39:9F:73:48:F0:BB:6F:89:9B:83:32:66:75:91:03:3B:9C"
      ]
    }
  }
]
```

> **IMPORTANT**: Include the SHA-256 fingerprints for **both** your local debug keystore and your **Google Play App Signing key** (found in Play Console -> Setup -> App Signing).

---

# 3. Expo & React Native Configuration

### Expo `app.json` Intent Filters

```json
{
  "expo": {
    "android": {
      "package": "com.example.mobileapp",
      "intentFilters": [
        {
          "action": "VIEW",
          "autoVerify": true,
          "data": [
            {
              "scheme": "https",
              "host": "example.com",
              "pathPrefix": "/item"
            }
          ],
          "category": ["BROWSABLE", "DEFAULT"]
        }
      ]
    }
  }
}
```

### Generated `AndroidManifest.xml` Excerpt

```xml
<intent-filter android:autoVerify="true">
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" android:host="example.com" android:pathPrefix="/item" />
</intent-filter>
```

---

# 4. Testing & Domain Verification CLI

To verify App Links domain verification status locally via Android Debug Bridge (ADB):

```bash
# Force domain verification check
adb shell pm verify-app-links --re-verify com.example.mobileapp

# Inspect domain verification status
adb shell pm get-app-links com.example.mobileapp

# Simulate opening an App Link
adb shell am start -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "https://example.com/item/123"
```

---

# 5. Operational Verification Checklist

- [ ] **HTTPS Hosting Verified**: `assetlinks.json` is served over HTTPS with content-type `application/json`.
- [ ] **Play Signing Fingerprint Included**: `assetlinks.json` includes the SHA-256 fingerprint from Google Play App Signing.
- [ ] **`autoVerify="true"` Configured**: `intentFilters` in `app.json` includes `"autoVerify": true`.
- [ ] **ADB Re-verify Passed**: `adb shell pm get-app-links` returns status `verified` for all domains.
- [ ] **Fallback Web Page Live**: `https://example.com/item/123` opens a functional web page if app is uninstalled.

---

# 6. Related Documentation

- [Universal Links Integration Guide](../universal-links/README.md) - iOS Universal Links.
- [Deep Links Integration Guide](../deep-links/README.md) - Custom URL schemes.
- [Release Preparation Workflow](../../ai/workflows/release-preparation.md) - Release prep checks.

---

# 7. Official Sources

- Android App Links Documentation: https://developer.android.com/training/app-links
- Statement List Generator & Tester: https://digitalassetlinks.googleapis.com/v1/statements:list

---

**Last verified:** August 13, 2026

