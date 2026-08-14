# Visual Media Asset Audit Handbook

This handbook details visual media asset audits, 1024x1024 Master App Icon alpha channel stripping, Android Adaptive Icon layers, 1320x2868 6.9" screenshot resolution matrices, and 1024x500 Feature Graphics for **Asset Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit all visual assets before store submission.

This guide is **not**:

- an authorization mechanism to upload screenshots containing alpha transparency
- a substitute for verifying canonical 6.9" display class screenshot dimensions
- a guide to using blurry or low-resolution app icons

---

# 1. Visual Asset Audit Matrix

```text
┌────────────────────────────────────────────────────────┐
│             VISUAL MEDIA ASSET AUDIT MATRIX            │
│                                                        │
│  - App Icon: 1024x1024 px PNG (No Alpha Transparency)  │
│  - iOS Screenshot: 1320x2868 px (6.9" Super Retina)    │
│  - Android Feature Graphic: 1024x500 px PNG / JPEG     │
│  - Android Screenshots: Min 1080 px side, Max 3840 px  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Free of Alpha Channels & Dimensionally Exact ]
```

---

# 2. Key Asset Verification Steps

1. **Alpha Channel Stripping**: Screenshots and App Icons MUST NOT contain alpha channels (`hasAlpha: NO`). Run `magick mogrify -alpha off *.png` to strip alpha channels.
2. **Canonical 6.9" iOS Display Class**: iPhone 16 Pro Max requires **1320 x 2868 px** (or 1290 x 2796 px) screenshots.
3. **Google Play Feature Graphic**: Must be exactly **1024 x 500 px** without promotional text ("#1 App", "Discount").

---

# 3. Operational Verification Checklist

- [ ] **Alpha Transparency Stripped**: All PNG images verified free of alpha channel transparency.
- [ ] **6.9" Screenshots Dimensionally Exact**: Screenshots match 1320x2868 px resolution.
- [ ] **Feature Graphic Uploaded**: 1024x500 px Feature Graphic prepared for Play Console.

---

# 4. Official Sources

- Apple Screenshot Specifications: https://developer.apple.com/help/app-store-connect/#/dev2161271f2

---

**Last verified:** August 14, 2026
