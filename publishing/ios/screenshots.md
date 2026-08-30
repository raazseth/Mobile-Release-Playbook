# iOS Visual Assets & Screenshot Specifications

This document covers pixel resolutions, device display classes, alpha channel stripping, and upload rules for **iOS Screenshots & App Icons** in App Store Connect — how to produce compliant screenshots for each iOS display class, including Apple's largest current display class, 6.9-inch Super Retina XDR. Screen sizes shift as new devices ship; verify the current display class matrix against [Apple's screenshot specifications](https://developer.apple.com/help/app-store-connect/#/devd274dd925) before relying on the numbers below.

This guide is **not**:

- a graphic design tutorial
- an authorization mechanism to upload screenshots containing alpha channel transparency
- a substitute for stripping alpha transparency channels from PNG exports

---

# 1. Apple App Store Screenshot Dimension Matrix (2026)

Apple requires screenshots for canonical device display classes. App Store Connect automatically scales down the 6.9-inch screenshot set for smaller iPhone models.

```text
6.9" display (largest current class):   portrait 1320x2868 or 1290x2796
6.5" display (legacy fallback bucket):  portrait 1242x2688 or 1284x2778
13" iPad Pro display:                   portrait 2064x2752 or 2048x2732
        │
        ↓
Upload up to 10 screenshots per device class in App Store Connect
```

| Display Class | Portrait Dimensions (Pixels) | Landscape Dimensions (Pixels) | Max Screenshots | Alpha Channel Allowed? |
|---|---|---|---|---|
| **6.9" Super Retina XDR** | **1320 x 2868** or **1290 x 2796** | **2868 x 1320** or **2796 x 1290** | 10 screenshots | **NO (Alpha Rejected)** |
| **6.5" Super Retina** | **1242 x 2688** or **1284 x 2778** | **2688 x 1242** or **2778 x 1284** | 10 screenshots | **NO (Alpha Rejected)** |
| **13" iPad Pro** | **2064 x 2752** or **2048 x 2732** | **2752 x 2064** or **2732 x 2048** | 10 screenshots | **NO (Alpha Rejected)** |

> **CRITICAL UPLOAD RULE**: Image exports MUST NOT contain alpha channels (transparency). Uploading PNG files with alpha transparency will trigger an instant `ITMS-90032` App Store Connect upload failure.

---

# 2. Master App Icon (1024 x 1024) Specifications

- **Dimensions**: **1024 x 1024 pixels** (24-bit PNG, 72 dpi).
- **No Alpha Channel**: Must not contain alpha transparency channels.
- **No Manual Corner Radius**: Provide a square image; Apple automatically applies the standard iOS icon corner radius and shadow.

---

# 3. Operational Verification Checklist

- [ ] **Canonical 6.9" Set Exported**: 1320 x 2868 px portrait set generated for latest iPhone display class.
- [ ] **13" iPad Set Exported**: 2064 x 2752 px set generated if app supports iPad.
- [ ] **Alpha Channels Stripped**: All PNG exports verified free of alpha transparency (`hasAlpha: NO`).
- [ ] **Master Icon 1024x1024 Verified**: App icon uploaded without alpha transparency or manual corner rounding.
- [ ] **UI Content Authentic**: Screenshots showcase actual app screens, not external promotional camera shots.

---

# Related documentation

### Publishing (iOS)

- `publishing/ios/README.md`
- `publishing/ios/app-review.md`
- `publishing/ios/app-store-connect.md`
- `publishing/ios/build-upload.md`
- `publishing/ios/metadata.md`
- `publishing/ios/production-release.md`
- `publishing/ios/testflight.md`

### Store accounts

- `store-accounts/app-store-connect.md`

### iOS signing

- `signing/ios/README.md`
- `signing/ios/distribution.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/ios.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Apple App Store Screenshot Specifications: https://developer.apple.com/help/app-store-connect/#/devd274dd925

---

**Last verified:** August 14, 2026

