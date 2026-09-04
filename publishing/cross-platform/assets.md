# Cross-Platform Visual Assets

Expo and React Native generate all the native icon and splash-screen formats from a handful of source images. This covers what those source assets need to look like and how Android's adaptive icon system differs from iOS's single flat icon.

This guide is **not**:

- an authorization mechanism to embed un-optimized, high-resolution PNGs directly in the JS bundle
- a guide to manually editing images across dozens of native resolution folders
- a substitute for verifying your Android adaptive icon's background/foreground layering actually looks right

---

## 1. From one source image to every native format

```text
Master assets in ./assets/
  - app icon:            1024x1024 px PNG, no alpha
  - adaptive foreground:  1024x1024 px transparent PNG
  - splash screen:       2048x2048 px PNG
        │
        ↓ (Expo prebuild processor)
Native asset generation
  - iOS: AppIcon.appiconset (@1x, @2x, @3x)
  - Android: mipmap-hdpi, mipmap-xhdpi, ... plus a monochrome vector icon for Android 13+
```

## 2. App icons and Android's adaptive icon

Android 8.0+ (API 26+) requires **adaptive icons** — a background layer plus a foreground vector or PNG layer, instead of a single flat image. Android 13+ (API 33+) adds a **monochrome themed icon** on top of that.

```json
{
  "expo": {
    "name": "FitnessTracker",
    "icon": "./assets/icon.png",
    "ios": {
      "icon": "./assets/icon-ios.png"
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#1E40AF",
        "monochromeImage": "./assets/monochrome-icon.png"
      }
    }
  }
}
```

| Asset | Resolution | Format | Transparency | Notes |
|---|---|---|---|---|
| iOS master app icon | 1024×1024 | 24-bit PNG | Not allowed | A plain square — iOS applies the rounded corners itself |
| Android adaptive foreground | 1024×1024 | 32-bit PNG | Required | Keep the foreground element inside the 66dp inner safe zone |
| Android monochrome icon | 1024×1024 | Vector SVG or flat PNG | Required | For Android 13+ material themed icons |
| Universal splash screen | 2048×2048 | PNG | Optional | Center the logo within a 1024×1024 safe viewport |

## 3. Avoiding a white flash on launch

`expo-splash-screen` keeps the native splash visible while your JS bundle and resources load, instead of showing an un-styled white screen for a moment:

```typescript
import * as SplashScreen from 'expo-splash-screen';
import { useEffect } from 'react';

// Keep native splash screen visible while loading resources
SplashScreen.preventAutoHideAsync();

export function AppRoot() {
  useEffect(() => {
    async function prepareApp() {
      try {
        // Pre-load fonts, state, and assets
        await loadAppResources();
      } finally {
        // Hide splash screen once UI is ready
        await SplashScreen.hideAsync();
      }
    }
    prepareApp();
  }, []);

  return <MainNavigator />;
}
```

## 4. Before you ship

- [ ] The master iOS icon has no alpha transparency.
- [ ] The Android adaptive foreground still reads correctly when cropped to a circle or squircle.
- [ ] A monochrome icon is provided for Android 13+ themed icons.
- [ ] The splash screen is locked from auto-hiding until the app is actually ready.
- [ ] Cold start doesn't show an un-styled white flash before the splash appears.

---

## Official sources

- Expo app icons guide: https://docs.expo.dev/guides/app-icons/
- Android adaptive icons overview: https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
- `publishing/cross-platform/metadata.md`
- `publishing/cross-platform/release-notes.md`
- `publishing/cross-platform/submission.md`

### Publishing (iOS)

- `publishing/ios/screenshots.md`

### Publishing (Android)

- `publishing/android/screenshots.md`

### Checklists

- `checklists/cross-platform.md`

### Store operations

- `store-operations/README.md`
