# Mobile Integrations Engineering

This directory contains production handbook guides, architecture patterns, native configuration schemas, privacy manifest declarations (`PrivacyInfo.xcprivacy` and Google Data Safety), permission requirements, and store review compliance rules for **Mobile Platform Integrations** in React Native and Expo applications.

It provides step-by-step guidance for configuring 13 core native capabilities and third-party SDK services while ensuring compliance with Apple App Store Review Guidelines, Google Play Developer Policies, and modern OS security models.

This directory is **not**:

- a software library, React Native package, or Expo plugin repository
- a substitute for reading official SDK documentation from platform vendors
- a bypass for native OS permission prompts or store review guidelines

---

# 1. Architecture of the Integrations Subsystem

Mobile integration engineering bridges cross-platform JavaScript application logic with native iOS (Swift/Objective-C) and Android (Kotlin/Java) APIs and cloud services.

```text
┌────────────────────────────────────────────────────────┐
│             CROSS-PLATFORM JAVASCRIPT LAYER            │
│  (React Native Core, Expo Modules, SDK API Wrappers)   │
└──────────────────────────┬─────────────────────────────┘
                           │
             [ Config Plugins & Entitlements ]
                           │
    ┌──────────────────────┼──────────────────────┐
    ▼                      ▼                      ▼
┌───────────────┐  ┌───────────────┐  ┌───────────────┐
│ iOS Native    │  │ Android Native│  │ Third-Party   │
│ Capabilities  │  │ Capabilities  │  │ Cloud Services│
└───────┬───────┘  └───────┬───────┘  └───────┬───────┘
        │                  │                  │
        ▼                  ▼                  ▼
 [ StoreKit 2,   ]  [ Play Billing,]  [ Sentry, FCM,  ]
 [ APNs, AASA    ]  [ AssetLinks   ]  [ AppsFlyer,    ]
 [ PrivacyInfo   ]  [ WorkManager  ]  [ Stripe APIs   ]
```

---

# 2. Integrations Directory Taxonomy

| Integration Area | Primary Focus & Capabilities | Key Platform & Store Guidelines | Fleshed-Out Guide |
|---|---|---|---|
| **[analytics/](analytics/README.md)** | Event tracking, behavioral telemetry (Segment, Mixpanel, Amplitude, Firebase). | App Tracking Transparency (ATT), Apple Privacy Manifests, Google Data Safety form alignment. | [analytics/README.md](analytics/README.md) |
| **[app-links/](app-links/README.md)** | Android deep linking via domain verification (`assetlinks.json`). | SHA-256 fingerprint verification, `autoVerify="true"` intent filters in `AndroidManifest.xml`. | [app-links/README.md](app-links/README.md) |
| **[attribution/](attribution/README.md)** | Mobile Measurement Partners (AppsFlyer, Branch, Adjust, Singular). | SKAdNetwork (SKAN 4.0 / 5.0) ID registration, ATT prompt timing, conversion value schemas. | [attribution/README.md](attribution/README.md) |
| **[background-processing/](background-processing/README.md)** | Background execution, periodic sync, deferred tasks. | iOS Background Modes (`fetch`, `processing`), Android WorkManager, battery optimization rules. | [background-processing/README.md](background-processing/README.md) |
| **[camera/](camera/README.md)** | Camera capture, barcode scanning, photo library access. | `NSCameraUsageDescription`, `NSMicrophoneUsageDescription`, Android 14+ photo picker permissions. | [camera/README.md](camera/README.md) |
| **[crash-reporting/](crash-reporting/README.md)** | Crash & error monitoring (Sentry, Firebase Crashlytics). | `.dSYM` upload, ProGuard/R8 mapping files, Hermes JS stack trace symbolication, PII masking. | [crash-reporting/README.md](crash-reporting/README.md) |
| **[deep-links/](deep-links/README.md)** | Custom URL schemes (`myapp://`). | Scheme registration in `app.json` / `Info.plist`, React Navigation linking configuration, security. | [deep-links/README.md](deep-links/README.md) |
| **[location/](location/README.md)** | Foreground and background location tracking, geofencing. | `NSLocationWhenInUseUsageDescription`, Google Play Background Location declaration & review policy. | [location/README.md](location/README.md) |
| **[maps/](maps/README.md)** | Map rendering, spatial markers (Apple Maps, Google Maps SDK, Mapbox). | API key restriction (preventing exposed keys in JS bundles), Android Manifest metadata. | [maps/README.md](maps/README.md) |
| **[oauth/](oauth/README.md)** | Authentication (Sign in with Apple, Google Sign-In, OAuth 2.0 PKCE). | Apple Guideline 4.0 (Sign in with Apple mandatory if third-party OAuth used), Keychain token storage. | [oauth/README.md](oauth/README.md) |
| **[payments/](payments/README.md)** | In-App Purchases, subscriptions, digital goods (StoreKit 2, Play Billing 7.0+, RevenueCat, Stripe). | Apple Guideline 3.1.1 (IAP mandatory for digital goods), StoreKit 2 server notifications, Google Play Billing. | [payments/README.md](payments/README.md) |
| **[push-notifications/](push-notifications/README.md)** | Remote push notifications (APNs, FCM HTTP v1, Expo Notifications, OneSignal). | APNs key (`.p8`), `aps-environment` entitlement, FCM HTTP v1 Service Account JSON, notification channels. | [push-notifications/README.md](push-notifications/README.md) |
| **[universal-links/](universal-links/README.md)** | iOS seamless deep linking via domain verification. | `apple-app-site-association` (AASA) JSON file hosting over HTTPS, Associated Domains entitlement. | [universal-links/README.md](universal-links/README.md) |

---

# 3. Universal Integration Rules

All platform integrations in this playbook must adhere to five mandatory engineering rules:

```text
1. Expo Config Plugins Over Manual Native Modification
   Prefer Expo Config Plugins (app.json / app.config.js) for native iOS (Info.plist,
   entitlements) and Android (AndroidManifest.xml, build.gradle) modifications to ensure
   reproducible native builds (`npx expo prebuild --clean`).

2. Explicit Privacy Manifest & Required Reason Declarations
   Every integration using sensitive APIs (disk stat, user defaults, timestamp, boot time)
   must declare exact API categories in `ios/PrivacyInfo.xcprivacy` and align with Google
   Play Data Safety declarations (Apple Guideline 5.1.1).

3. Zero Exposure of Third-Party API Keys
   Secrets (payment secret keys, service account JSONs, APNs private keys) MUST
   NEVER be stored in mobile app binary code or JavaScript bundles. Use public client keys
   and backend proxy endpoints.

4. Dynamic Permission Request Timing
   Never request native permissions (Location, Camera, Push Notifications, ATT tracking)
   on initial app launch without prior user context. Request permissions in-context when
   the user triggers the feature.

5. Graceful Degradation & Offline Resilience
   Integrations must handle network offline states, denied permissions, and missing native
   capabilities gracefully without crashing the app.
```

---

# 4. Related Documentation

- [Repository Architecture](../ARCHITECTURE.md) - Full repository structure.
- [Mobile Release Lifecycle](../foundations/mobile-release-lifecycle.md) - Lifecycle release phases.
- [AI Subsystem](../ai/README.md) - AI-assisted release engineering.
- [Signing & Credentials](../signing/README.md) - Certificates and provisioning profiles.

---

# 5. Official Sources

- Apple Developer Documentation: https://developer.apple.com/documentation/
- Android Developer Documentation: https://developer.android.com/
- Expo Integration Guides: https://docs.expo.dev/guides/
- React Native Core Guides: https://reactnative.dev/docs/getting-started

---

**Last verified:** August 13, 2026

