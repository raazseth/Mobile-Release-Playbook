# Android Screenshots and Graphic Assets

This covers the pixel dimensions, aspect ratios, and asset counts Google Play Console requires for phones, 7-inch and 10-inch tablets, and the Feature Graphic.

This guide is **not**:

- a graphic design tutorial
- an authorization mechanism to upload screenshots with alpha-channel transparency or transparent backgrounds
- a substitute for providing the mandatory Feature Graphic (1024×500)

---

## 1. What Google Play requires

```text
App icon:              512x512 px, 32-bit PNG, max 1024 KB
Feature graphic:       1024x500 px, JPEG or 24-bit PNG
Phone screenshots:     min 2, max 8, 320px-3840px per side
Tablet screenshots:    min 2 per size class (7-inch, 10-inch)
        │
        ↓
Published on the Google Play Store listing page
```

| Asset | Dimensions | Aspect ratio | Format / max size | Count |
|---|---|---|---|---|
| App icon | 512×512 | 1:1 | 32-bit PNG, max 1024 KB | 1, mandatory |
| Feature graphic | 1024×500 | 16:9 | JPEG or 24-bit PNG, max 15 MB | 1, mandatory |
| Phone screenshots | 320px–3840px per side | 16:9 or 9:16 recommended | JPEG or 24-bit PNG, max 8 MB | 2–8, mandatory |
| 7-inch tablet | 320px–3840px per side | 16:9 or 9:16 | JPEG or 24-bit PNG | 2+, needed for tablet eligibility |
| 10-inch tablet | 320px–3840px per side | 16:9 or 9:16 | JPEG or 24-bit PNG | 2+, needed for tablet eligibility |

## 2. Getting the Feature Graphic right

The Feature Graphic sits at the top of your listing and is used on store promotion cards across Google Play:

- Skip "Download Now" or pricing text — it's not allowed here.
- Keep logos and important text inside the center safe zone, since the graphic gets cropped differently on smaller screens.
- Don't add an artificial border or frame around the 1024×500 canvas.

## 3. Before you submit

- [ ] The app icon is a 512×512, 32-bit PNG.
- [ ] The Feature Graphic is exactly 1024×500.
- [ ] At least 2 phone screenshots are uploaded, in 16:9 or 9:16.
- [ ] Dedicated 7-inch and 10-inch tablet screenshots are provided if you want tablet eligibility.
- [ ] PNG exports are checked for unnecessary alpha transparency.

---

## Official sources

- Google Play graphic assets specifications: https://support.google.com/googleplay/android-developer/answer/9866151

**Last verified:** August 14, 2026

---

## Related documentation

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
