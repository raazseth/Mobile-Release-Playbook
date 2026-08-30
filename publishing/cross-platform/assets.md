# Cross-Platform Visual Asset Pipelines & App Icons

This document covers visual asset resolutions, Android Adaptive Icon layers, and splash screen configuration for **Cross-Platform Visual Assets** in Expo and React Native applications — how to generate app icons and splash screens for both iOS and Android from a single source asset.

This guide is **not**:

- an authorization mechanism to embed un-optimized high-resolution PNG assets directly in the JS bundle
- a guide to manual image editing across dozens of native resolution folders
- a substitute for verifying Android Adaptive Icon background/foreground layering

---

# 1. Cross-Platform Asset Architecture & Expo Image Engine

Expo and React Native manage visual assets using source master images processed automatically during native build compilation.

```text
Master assets in ./assets/
  - app icon:          1024x1024 px PNG, no alpha
  - adaptive foreground: 1024x1024 px transparent PNG
  - splash screen:     2048x2048 px PNG
        │
        ↓ (Expo prebuild processor)
Native asset generation
  - iOS: AppIcon.appiconset (@1x, @2x, @3x)
  - Android: mipmap-hdpi, mipmap-xhdpi, ... plus a monochrome vector icon for Android 13+
```

---

# 2. App Icon & Android Adaptive Icon Specifications

Android 8.0+ (API 26+) mandates **Adaptive Icons**, which split the icon into a background layer and a foreground vector/PNG layer. Android 13+ (API 33+) adds **Monochromatic Themed Icons**.

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

### Asset Dimension Requirements

| Asset Type | Master Resolution (Pixels) | Format | Transparency (Alpha) | Notes |
|---|---|---|---|---|
| **iOS Master App Icon** | **1024 x 1024** | 24-bit PNG | **NO ALPHA ALLOWED** | Square without rounded corners (iOS applies radius automatically). |
| **Android Adaptive Foreground** | **1024 x 1024** | 32-bit PNG | **Alpha Required** | Foreground element centered within 66dp inner safe zone. |
| **Android Monochrome Icon** | **1024 x 1024** | Vector SVG / Flat PNG | **Alpha Required** | Monochromatic icon for Android 13+ material themed icons. |
| **Universal Splash Screen** | **2048 x 2048** | PNG | Optional | Centered logo within 1024x1024 safe viewport. |

---

# 3. Expo Splash Screen Configuration (`expo-splash-screen`)

Prevent white screen flashes during app initialization using native splash screen locking:

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

---

# 4. Operational Verification Checklist

- [ ] **1024x1024 Master Icon Clean**: Master iOS icon free of alpha transparency channels (`hasAlpha: NO`).
- [ ] **Adaptive Icon Safe Zone Verified**: Foreground element remains visible when cropped to circle/squircle.
- [ ] **Android Monochrome Icon Included**: Monochromatic vector icon provided for Android 13+ material themes.
- [ ] **Splash Screen Lock Implemented**: Native splash screen prevented from auto-hiding during app boot.
- [ ] **No White Flash on Launch**: Cold start launch verified smooth without un-styled white view flashes.

---

# Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
- `publishing/cross-platform/metadata.md`
- `publishing/cross-platform/release-notes.md`
- `publishing/cross-platform/submission.md`

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Checklists

- `checklists/cross-platform.md`

### Store operations

- `store-operations/README.md`

---

# Official sources

- Expo App Icons Guide: https://docs.expo.dev/guides/app-icons/
- Android Adaptive Icons Overview: https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive

---

**Last verified:** August 14, 2026

