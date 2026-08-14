# Deep Links, Universal Links & App Links Testing

This document details deep link testing protocols, Universal Links (iOS), Android App Links, CLI verification commands (`simctl openurl`, `adb am start`), and AASA domain validation for **Deep Links Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test incoming deep link navigation from cold start and warm state viewports.

This guide is **not**:

- an authorization mechanism to skip AASA domain validation
- a substitute for testing cold start deep link navigation
- a guide to hardcoding deep link routes inside native code

---

# 1. Deep Link Architecture & Trigger Mechanisms

Deep links routes users directly to specific screens inside the application, passing route parameters from external web pages, emails, or push notifications.

```text
┌────────────────────────────────────────────────────────┐
│             DEEP LINK ROUTING ARCHITECTURE             │
│                                                        │
│  - Custom URL Scheme: `mobileapp://workout/123`        │
│  - iOS Universal Link: `https://example.com/workout/123`│
│  - Android App Link: `https://example.com/workout/123` │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Cold Start Launch ]               [ Warm Foreground Launch ]
App launches from scratch &         App receives `Linking` event &
navigates to target route           navigates immediately
```

---

# 2. CLI Deep Link Testing Commands

### iOS Universal Link / Custom Scheme Testing (`simctl`)

```bash
# Trigger Custom URL Scheme on iOS Simulator
xcrun simctl openurl booted "mobileapp://workout/123?referrer=email"

# Trigger Universal Link on iOS Simulator
xcrun simctl openurl booted "https://example.com/workout/123"
```

### Android App Link / Custom Scheme Testing (`adb`)

```bash
# Trigger Custom URL Scheme on Android Emulator
adb shell am start -W -a android.intent.action.VIEW -d "mobileapp://workout/123" com.example.mobileapp

# Trigger Android App Link on Android Emulator
adb shell am start -W -a android.intent.action.VIEW -d "https://example.com/workout/123" com.example.mobileapp
```

---

# 3. Operational Verification Checklist

- [ ] **Cold Start Navigates**: App launches from zero state and lands on target route correctly.
- [ ] **Warm Foreground Navigates**: App in background responds to deep link event and updates screen.
- [ ] **Parameters Parsed**: Route parameters (`workoutId=123`) extracted and populated in UI.
- [ ] **Fallback Executed**: Invalid deep link URL gracefully falls back to main screen.
- [ ] **AASA Verified**: `apple-app-site-association` file active at `https://example.com/.well-known/apple-app-site-association`.

---

# 4. Official Sources

- React Native Linking Documentation: https://reactnative.dev/docs/linking
- Expo Linking Guide: https://docs.expo.dev/guides/deep-linking/

---

**Last verified:** August 14, 2026
