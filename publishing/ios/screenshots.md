# iOS Visual Assets & Screenshot Specifications

This document details the pixel resolution matrices, device display classes, alpha channel transparency stripping rules, and upload standards for **iOS Screenshots & App Icons** in App Store Connect.

Engineered in alignment with **2026 platform specifications**—specifically including Apple's canonical **6.9-inch Super Retina XDR display class**—it specifies how to produce pixel-perfect, compliant screenshots for all iOS device classes.

This guide is **not**:

- a graphic design tutorial
- an authorization mechanism to upload screenshots containing alpha channel transparency
- a substitute for stripping alpha transparency channels from PNG exports

---

# 1. Apple App Store Screenshot Dimension Matrix (2026)

Apple requires screenshots for canonical device display classes. App Store Connect automatically scales down the 6.9-inch screenshot set for smaller iPhone models.

```text
┌────────────────────────────────────────────────────────┐
│             APPLE SCREENSHOT MATRIX (2026)             │
│                                                        │
│  - 6.9" Display (iPhone 16 Pro Max / 17 Pro Max):      │
│    Portrait: 1320 x 2868 px | 1290 x 2796 px          │
│                                                        │
│  - 6.5" Display (iPhone 11 Pro Max / XS Max Fallback): │
│    Portrait: 1242 x 2688 px | 1284 x 2778 px          │
│                                                        │
│  - 13" iPad Pro Display (iPad Pro 13-inch M4):         │
│    Portrait: 2064 x 2752 px | 2048 x 2732 px          │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Upload Up to 10 Screenshots per Device Class in App Store Connect ]
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

# 4. Related Documentation

- [Metadata Handbook](metadata.md) - Listing text metadata.
- [App Review Handbook](app-review.md) - Review submission.
- [Store Operations Screenshots](../../store-operations/screenshots.md) - Screenshot specifications.

---

# 5. Official Sources

- Apple App Store Screenshot Specifications: https://developer.apple.com/help/app-store-connect/#/devd274dd925

---

**Last verified:** August 14, 2026
