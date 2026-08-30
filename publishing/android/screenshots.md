# Android Graphic Assets & Screenshot Specifications

This document covers pixel resolutions, aspect ratios, asset counts, and Feature Graphic rules for **Android Screenshots & Visual Assets** in Google Play Console — how to produce compliant visual assets for phones, 7-inch tablets, and 10-inch tablets.

This guide is **not**:

- a graphic design tutorial
- an authorization mechanism to upload screenshots containing alpha channel transparency or transparent backgrounds
- a substitute for providing mandatory Feature Graphics (1024x500)

---

# 1. Google Play Visual Asset Specifications Matrix

Google Play Console mandates specific graphic assets for store listing publication:

```text
App icon:              512x512 px, 32-bit PNG, max 1024 KB
Feature graphic:       1024x500 px, JPEG or 24-bit PNG
Phone screenshots:     min 2, max 8, 320px-3840px per side
Tablet screenshots:    min 2 per size class (7-inch, 10-inch)
        │
        ↓
Published on the Google Play Store listing page
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

# Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/app-review.md`
- `publishing/android/closed-testing.md`
- `publishing/android/internal-testing.md`
- `publishing/android/metadata.md`
- `publishing/android/play-console.md`
- `publishing/android/production-release.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Android signing

- `signing/android/README.md`
- `signing/android/play-app-signing.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Google Play Graphic Assets Specifications: https://support.google.com/googleplay/android-developer/answer/9866151

---

**Last verified:** August 14, 2026

