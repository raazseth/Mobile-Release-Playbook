# Android Graphic Assets & Screenshot Specifications

This document details the pixel resolutions, aspect ratios, asset counts, Feature Graphic guidelines, and upload standards for **Android Screenshots & Visual Assets** in Google Play Console.

Engineered in alignment with **2026 platform specifications**, it specifies how to produce compliant visual store assets for Android phones, 7-inch tablets, 10-inch tablets, Chromebooks, and Google Play Store features.

This guide is **not**:

- a graphic design tutorial
- an authorization mechanism to upload screenshots containing alpha channel transparency or transparent backgrounds
- a substitute for providing mandatory Feature Graphics (1024x500)

---

# 1. Google Play Visual Asset Specifications Matrix

Google Play Console mandates specific graphic assets for store listing publication:

```text
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY GRAPHIC ASSETS MATRIX          │
│                                                        │
│  - App Icon: 512 x 512 px (32-bit PNG, max 1024 KB)    │
│  - Feature Graphic: 1024 x 500 px (JPEG or 24-bit PNG)  │
│  - Phone Screenshots: Min 2, Max 8 (320px - 3840px)    │
│  - 7-inch & 10-inch Tablet Screenshots: Min 2 per size │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Published on Google Play Store App Details Page ]
```

| Asset Type | Required Dimensions (Pixels) | Aspect Ratio | Format / Max Size | Required Count |
|---|---|---|---|---|
| **App Icon** | **512 x 512** | 1:1 Square | 32-bit PNG, max 1024 KB | 1 Asset (Mandatory) |
| **Feature Graphic** | **1024 x 500** | 16:9 Landscape | JPEG or 24-bit PNG, max 15 MB | 1 Asset (Mandatory) |
| **Phone Screenshots** | Min side **320px**, Max side **3840px** | 16:9 or 9:16 recommended | JPEG or 24-bit PNG, max 8 MB | **Min 2, Max 8** (Mandatory) |
| **7-inch Tablet** | Min side **320px**, Max side **3840px** | 16:9 or 9:16 | JPEG or 24-bit PNG | Min 2 (For Tablet Eligibility) |
| **10-inch Tablet** | Min side **320px**, Max side **3840px** | 16:9 or 9:16 | JPEG or 24-bit PNG | Min 2 (For Tablet Eligibility) |

---

# 2. Feature Graphic (1024 x 500) Design Rules

The Feature Graphic appears at the top of your Google Play Store listing page and is used for store promotion cards across Google Play:

- **No Call-To-Action Text**: Do not include text like "Download Now" or pricing information.
- **Center Key Visuals**: Keep important brand logos and text within the center safe zone to avoid cutoff when rendered on smaller screen viewports.
- **No Bordering**: Do not add artificial borders or frames to the 1024x500 canvas.

---

# 3. Operational Verification Checklist

- [ ] **App Icon 512x512 Uploaded**: High-resolution 512x512 32-bit PNG app icon provided.
- [ ] **Feature Graphic 1024x500 Uploaded**: Feature graphic created at exact 1024x500 px dimensions.
- [ ] **Phone Screenshots Set (Min 2)**: At least 2 phone screenshots provided (16:9 or 9:16 aspect ratio).
- [ ] **Tablet Sets Provided**: Dedicated 7-inch and 10-inch tablet screenshots provided for tablet readiness.
- [ ] **Alpha Transparency Stripped**: PNG exports verified free of un-needed alpha transparency.

---

# 4. Related Documentation

- [Metadata Handbook](metadata.md) - Listing text metadata.
- [Play Console Handbook](play-console.md) - Console setup.
- [Store Operations Screenshots](../../store-operations/screenshots.md) - iOS & Android screenshot matrices.

---

# 5. Official Sources

- Google Play Graphic Assets Specifications: https://support.google.com/googleplay/android-developer/answer/9866151

---

**Last verified:** August 14, 2026
