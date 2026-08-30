# iOS Universal Links Integration

This guide details the integration architecture, `apple-app-site-association` (AASA) JSON file hosting specifications, Associated Domains entitlement, and release testing protocols for **iOS Universal Links** in React Native and Expo applications.

Unlike custom scheme deep links (`myapp://`), Universal Links open web HTTPS URLs (`https://example.com/item/123`) seamlessly inside the installed iOS application without opening Safari or displaying an un-verified URL prompt.

This guide is **not**:

- an Android App Links guide (see [app-links/](../app-links/README.md))
- a custom scheme deep linking guide (see [deep-links/](../deep-links/README.md))
- a substitute for hosting AASA files over secure HTTPS endpoints with valid SSL certificates

---

# 1. Integration Architecture & Domain Association

Universal Links utilize two-way domain verification between the iOS application binary and the HTTPS web domain.

```text
[ User Clicks HTTPS Link: `https://example.com/item/123` ]
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│              iOS OS UNIVERSAL LINK ROUTER              │
│  - Checks Associated Domains entitlement in app binary │
│  - Verifies `https://example.com/.well-known/apple-app-site-association`
│  - Matches App ID (`ABCDE12345.com.example.mobileapp`) │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ App Installed & Match Found ]     [ App Not Installed ]
 Open App Directly via               Open Fallback Web Page
 Native Navigation                   inside Safari Browser
```

---

# 2. `apple-app-site-association` (AASA) File Specification

The domain web server must host a valid AASA JSON file at either of the following paths:

```text
https://example.com/.well-known/apple-app-site-association
https://example.com/apple-app-site-association
```

### AASA File JSON Format (Modern Format)

```json
{
  "applinks": {
    "details": [
      {
        "appIDs": [ "ABCDE12345.com.example.mobileapp" ],
        "components": [
          {
            "/": "/item/*",
            "comment": "Matches all item detail pages"
          },
          {
            "/": "/user/*",
            "comment": "Matches user profile pages"
          },
          {
            "/": "/buy/*",
            "exclude": true,
            "comment": "Excludes checkout flow from deep linking"
          }
        ]
      }
    ]
  }
}
```

> **IMPORTANT**: Replace `ABCDE12345` with your 10-character Apple Developer Team ID, followed by a dot and your app's bundle identifier (`com.example.mobileapp`).

### Web Server Hosting Requirements

- **HTTPS Mandate**: The AASA file MUST be served over HTTPS with a valid, trusted SSL certificate.
- **Content-Type**: Serve with `Content-Type: application/json`.
- **No Redirects**: The AASA URL must return HTTP status `200 OK` directly without HTTP redirects.

---

# 3. Expo & React Native Associated Domains Entitlement

Add the `applinks:` prefix and domain to `associatedDomains` in Expo `app.json`:

```json
{
  "expo": {
    "ios": {
      "bundleIdentifier": "com.example.mobileapp",
      "associatedDomains": [
        "applinks:example.com",
        "applinks:www.example.com"
      ]
    }
  }
}
```

### Generated Entitlements File Excerpt (`ios/MobileApp/MobileApp.entitlements`)

```xml
<key>com.apple.developer.associated-domains</key>
<array>
    <string>applinks:example.com</string>
    <string>applinks:www.example.com</string>
</array>
```

---

# 4. Testing & CLI Verification Commands

To test Universal Links resolution on iOS Simulators:

```bash
# Test opening Universal Link on iOS Simulator
xcrun simctl openurl booted "https://example.com/item/123"

# Verify AASA file validation via Apple API
curl -v "https://app-site-association.cdn-apple.com/a/v1/example.com"
```

---

# 5. Operational Verification Checklist

- [ ] **AASA Hosted over HTTPS**: `https://example.com/.well-known/apple-app-site-association` returns HTTP 200 OK directly.
- [ ] **App ID Accurate**: App ID in AASA matches `<TEAM_ID>.<BUNDLE_ID>`.
- [ ] **Associated Domains Entitlement Added**: `applinks:example.com` included in iOS entitlements.
- [ ] **Content-Type Correct**: Server responds with `application/json` header for AASA file.
- [ ] **Safari Fallback Live**: `https://example.com/item/123` opens a functional web page if app is uninstalled.

---

# 6. Related Documentation

- [App Links Integration Guide](../app-links/README.md) - Android App Links.
- [Deep Links Integration Guide](../deep-links/README.md) - Custom URL schemes.
- [OAuth Integration Guide](../oauth/README.md) - Social login authentication.

---

# 7. Official Sources

- Apple Universal Links Documentation: https://developer.apple.com/ios/universal-links/
- Apple Supporting Associated Domains: https://developer.apple.com/documentation/xcode/supporting-associated-domains

---

**Last verified:** August 13, 2026

