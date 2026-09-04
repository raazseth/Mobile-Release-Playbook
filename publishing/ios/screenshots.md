# iOS Screenshots and App Icon

This covers the pixel dimensions, device display classes, and alpha-channel rules for App Store Connect screenshots and app icons — including Apple's largest current display class, the 6.9-inch Super Retina XDR. Screen sizes shift as new devices ship, so check the current display class matrix against [Apple's screenshot specifications](https://developer.apple.com/help/app-store-connect/#/devd274dd925) before relying on the numbers below.

This guide is **not**:

- a graphic design tutorial
- an authorization mechanism to upload screenshots with alpha-channel transparency
- a substitute for actually stripping the alpha channel from your PNG exports

---

## 1. Screenshot dimensions by device class

App Store Connect requires screenshots for each canonical display class, and it automatically scales the 6.9-inch set down for smaller iPhone models.

```text
6.9" display (largest current class):   portrait 1320x2868 or 1290x2796
6.5" display (legacy fallback bucket):  portrait 1242x2688 or 1284x2778
13" iPad Pro display:                   portrait 2064x2752 or 2048x2732
        │
        ↓
Up to 10 screenshots per device class
```

| Display class | Portrait | Landscape | Max screenshots | Alpha channel |
|---|---|---|---|---|
| 6.9" Super Retina XDR | 1320×2868 or 1290×2796 | 2868×1320 or 2796×1290 | 10 | Not allowed |
| 6.5" Super Retina | 1242×2688 or 1284×2778 | 2688×1242 or 2778×1284 | 10 | Not allowed |
| 13" iPad Pro | 2064×2752 or 2048×2732 | 2752×2064 or 2732×2048 | 10 | Not allowed |

> **Important:** Images with alpha transparency get rejected on upload with an `ITMS-90032` error. Strip the alpha channel before exporting.

## 2. App icon (1024×1024)

- **1024×1024 pixels**, 24-bit PNG, 72 dpi.
- No alpha channel.
- Upload a plain square — Apple applies the rounded corners and shadow automatically, so don't add them yourself.

## 3. Before you upload

- [ ] The 6.9" set (1320×2868 portrait) is exported for the current largest display class.
- [ ] The 13" iPad set is exported if the app supports iPad.
- [ ] Every PNG export is confirmed free of alpha transparency.
- [ ] The 1024×1024 app icon is uploaded without alpha or manual corner rounding.
- [ ] Screenshots show actual app screens, not promotional composites shot outside the app.

---

## Official sources

- Apple App Store screenshot specifications: https://developer.apple.com/help/app-store-connect/#/devd274dd925

**Last verified:** August 14, 2026

---

## Related documentation

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
