# Maps & Spatial Services Integration

This guide details the integration architecture, SDK provider selection (Apple Maps, Google Maps SDK, Mapbox), API key security, and native manifest configuration for **Maps & Spatial Services** in React Native and Expo applications.

It specifies how to render interactive map views, custom markers, and vector tiles while safeguarding API keys against public repository leakages.

This guide is **not**:

- a location tracking guide (see [location/](../location/README.md))
- an authorization mechanism to embed unrestricted administrative API keys in client binaries
- a substitute for configuring Google Maps API restriction rules

---

# 1. Integration Architecture & SDK Selection

Mobile applications render spatial map interfaces using native map components (Apple Maps on iOS via MapKit; Google Maps or Mapbox on Android and iOS).

```text
┌────────────────────────────────────────────────────────┐
│                   REACT NATIVE MAP VIEW                │
│    (react-native-maps / @rnmapbox/maps component)      │
└──────────────────────────┬─────────────────────────────┘
                           │
    ┌──────────────────────┴──────────────────────┐
    ▼                                             ▼
┌────────────────────────┐               ┌────────────────────────┐
│   iOS Native MapKit    │               │  Android Google Maps   │
│ (Apple Maps - No Key)  │               │ (Google Maps SDK Key)  │
└────────────────────────┘               └────────────────────────┘
```

---

# 2. API Key Security & Restriction Rules

Google Maps and Mapbox SDKs require API keys. Embedding un-restricted API keys in mobile app JavaScript bundles exposes your developer account to unauthorized billing usage.

```text
┌────────────────────────────────────────────────────────┐
│                 CRITICAL SECURITY RULE                 │
│                                                        │
│   Google Maps and Mapbox API keys embedded in mobile   │
│   apps MUST be restricted to explicit Android package  │
│   names (with SHA-1 fingerprints) and iOS Bundle IDs.  │
└────────────────────────────────────────────────────────┘
```

### Required API Key Restrictions in Google Cloud Console

1. **Application Restrictions**:
   - iOS: Restrict key usage to your app's bundle identifier (`com.example.mobileapp`).
   - Android: Restrict key usage to your app's package name (`com.example.mobileapp`) and SHA-1 signing fingerprint.
2. **API Restrictions**: Restrict key to **Maps SDK for Android** and **Maps SDK for iOS** only. Disable Geocoding, Places, or Direction APIs on client-embedded keys.

---

# 3. Expo & React Native Manifest Configuration

### Expo `app.json` Maps Configuration

```json
{
  "expo": {
    "ios": {
      "config": {
        "usesNonExemptEncryption": false
      }
    },
    "android": {
      "config": {
        "googleMaps": {
          "apiKey": "YOUR_RESTRICTED_GOOGLE_MAPS_API_KEY"
        }
      }
    }
  }
}
```

### Generated `AndroidManifest.xml` Metadata Excerpt

```xml
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="YOUR_RESTRICTED_GOOGLE_MAPS_API_KEY" />
```

---

# 4. Operational Verification Checklist

- [ ] **Google Maps Key Restricted**: Google Cloud Console API key restricted to package name and SHA-1 fingerprint.
- [ ] **iOS Bundle ID Restricted**: iOS API key restricted to bundle identifier (`com.example.mobileapp`).
- [ ] **Un-needed APIs Disabled**: Geocoding and Directions APIs disabled on client-embedded keys (proxied via backend server).
- [ ] **Tile Performance Verified**: Map view renders fluidly without tile flickering or memory leaks.
- [ ] **No Admin Keys Embedded**: Administrative or unrestricted GCP keys are verified absent from client code.

---

# 5. Related Documentation

- [Location Services Guide](../location/README.md) - Location tracking.
- [Secret Protection](../../ai/security/secret-protection.md) - Secret protection rules.
- [Release Audit Workflow](../../ai/workflows/release-audit.md) - Pre-release audits.

---

# 6. Official Sources

- Google Maps SDK for Android Key Restrictions: https://developers.google.com/maps/documentation/android-sdk/get-api-key
- Mapbox React Native SDK: https://github.com/rnmapbox/maps

---

**Last verified:** August 13, 2026
