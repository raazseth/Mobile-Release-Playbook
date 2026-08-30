# Screenshot Specifications, Dimensions & Safety Zones Handbook

This handbook details the exact pixel resolution matrices, alpha channel stripping rules, safe design zones, device class requirements, and upload standards for **App Store & Google Play Screenshots** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**—specifically Apple's canonical **6.9-inch Super Retina XDR display class**—it provides exhaustive technical and operational guidance for producing pixel-perfect, compliant screenshots for all mobile device classes.

This guide is **not**:

- a generic graphic design tutorial
- an authorization mechanism to upload mockups featuring un-released hardware frames
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

# 2. Google Play Store Screenshot Specifications

Google Play Console enforces flexible dimension boundaries while requiring specific asset counts:

- **Phone Screenshots**: Minimum **2 screenshots**, maximum **8 screenshots** per device type. Minimum side length: 320px; maximum side length: 3840px. Aspect ratio 16:9 or 9:16 recommended.
- **7-inch & 10-inch Tablets**: Minimum 2 screenshots for tablet listing eligibility.
- **Feature Graphic**: **1024 x 500 pixels** (JPEG or 24-bit PNG, max 15MB).

---

# 3. Screenshot Visual Design Safe Zones

To ensure screenshot text headlines remain legible when displayed in store search results and product pages:

```text
┌────────────────────────────────────────────────────────┐
│             SCREENSHOT DESIGN SAFE ZONES               │
│                                                        │
│  ┌──────────────────────────────────────────────────┐  │
│  │ TOP SAFE ZONE (Headline Copy: 15% Height)        │  │
│  │ "Track Workouts in Real Time"                    │  │
│  ├──────────────────────────────────────────────────┤  │
│  │                                                  │  │
│  │ MIDDLE SAFE ZONE (App UI Capture: 70% Height)    │  │
│  │ [ Actual React Native Viewport Screenshot ]      │  │
│  │                                                  │  │
│  ├──────────────────────────────────────────────────┤  │
│  │ BOTTOM SAFE ZONE (Secondary Badge: 15% Height)   │  │
│  └──────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

---

# 4. CLI Automated Alpha Channel Stripping Command

Use ImageMagick or standard macOS CLI tools to strip alpha channels from exported PNG screenshots before uploading:

```bash
# Strip alpha transparency channel using ImageMagick
magick mogrify -alpha off ./screenshots/*.png

# Verify no alpha channels remain
sips -g hasAlpha ./screenshots/*.png
```

---

# 5. Fastlane Automated Screenshot Pipeline (`snapshot` & `frameit`)

Automate screenshot capture and device framing using **Fastlane Snapshot** and **Fastlane Frameit**:

```bash
# Capture iOS screenshots across multiple device simulators
bundle exec fastlane snapshot

# Add elegant device frames and localized text headlines
bundle exec fastlane frameit
```

---

# 6. Operational Verification Checklist

- [ ] **Canonical 6.9" Set Exported**: 1320 x 2868 px portrait set generated for latest iPhone display class.
- [ ] **13" iPad Set Exported**: 2064 x 2752 px set generated if app supports iPad.
- [ ] **Alpha Channels Stripped**: All PNG exports verified free of alpha transparency (`hasAlpha: NO`).
- [ ] **UI Content Authentic**: Screenshots showcase actual app screens, not external promotional camera shots.
- [ ] **Text Legible**: Headline text legible when rendered on small mobile screen viewports.
- [ ] **Google Feature Graphic Ready**: 1024 x 500 px feature graphic generated for Google Play.

---

# 7. Related Documentation

- [App Previews Handbook](app-previews.md) - Video preview specs.
- [Metadata Handbook](metadata.md) - Text metadata.
- [App Listing Handbook](app-listing.md) - Listing setup.

---

# 8. Official Sources

- Apple Screenshot Specifications: https://developer.apple.com/help/app-store-connect/#/devd274dd925
- Google Play Graphic Assets Specifications: https://support.google.com/googleplay/android-developer/answer/9866151

---

**Last verified:** August 14, 2026

