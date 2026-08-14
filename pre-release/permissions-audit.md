# Runtime Permissions Audit Handbook

This handbook details permission audits, `Info.plist` usage description strings, Android `AndroidManifest.xml` high-risk permission audits, and Android 14+ Selected Photos compliance for **Permissions Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit permission declarations to prevent store rejections.

This guide is **not**:

- an authorization mechanism to request unused high-risk permissions
- a substitute for providing descriptive usage strings in `Info.plist`
- a guide to omitting permission pre-prompts

---

# 1. Permission Audit Pipeline

```text
┌────────────────────────────────────────────────────────┐
│             PERMISSION AUDIT PIPELINE                  │
│                                                        │
│  - iOS: Audit `Info.plist` usage description strings   │
│  - Android: Audit `AndroidManifest.xml` permissions    │
│  - Verify Just-In-Time execution in App UI             │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Zero Un-Used High-Risk Permissions Requested ]
```

---

# 2. Key Permission Usage Strings (iOS `Info.plist`)

Verify all requested native permissions include clear, user-facing usage strings:

```xml
<key>NSCameraUsageDescription</key>
<string>This app requires access to your camera to scan QR codes and take profile photos.</string>

<key>NSLocationWhenInUseUsageDescription</key>
<string>This app uses your location to show nearby gym facilities on the interactive map.</string>
```

---

# 3. Operational Verification Checklist

- [ ] **Usage Strings Non-Generic**: `Info.plist` strings explain *why* permission is needed, avoiding generic text like "App needs camera".
- [ ] **No Unused Android Permissions**: High-risk permissions (e.g., `READ_SMS`, `PROCESS_OUTGOING_CALLS`) removed if unused.
- [ ] **Android 14+ Selected Photos**: `READ_MEDIA_VISUAL_USER_SELECTED` included in `AndroidManifest.xml`.

---

# 4. Official Sources

- Android Permissions Best Practices: https://developer.android.com/training/permissions/requesting

---

**Last verified:** August 14, 2026
