# iOS-Specific Testing & Simulator Automation

This document details Xcode Simulator automation (`xcrun simctl`), TestFlight beta validation, iOS 18 permission prompts testing, and memory leak detection for **iOS Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test iOS applications systematically on simulated and physical Apple hardware.

This guide is **not**:

- an authorization mechanism to skip testing on physical iOS hardware
- a guide to bypassing iOS permission dialog prompts
- a substitute for testing `PrivacyInfo.xcprivacy` manifests

---

# 1. Xcode Simulator Automation via `simctl`

Automate iOS Simulator lifecycle management, app installation, location simulation, and URL opening via `xcrun simctl`:

```bash
# Boot iOS 18 iPhone 16 Pro Simulator
xcrun simctl boot "iPhone 16 Pro"

# Install compiled .app build onto simulator
xcrun simctl install booted ./build/Build/Products/Debug-iphonesimulator/MobileApp.app

# Trigger Deep Link open
xcrun simctl openurl booted "mobileapp://workout/123"

# Simulate Geolocation Coordinates (Cupertino, CA)
xcrun simctl location booted set 37.33182 -122.03118

# Capture Simulator Screenshot
xcrun simctl io booted screenshot ./screenshots/ios_screen_1.png
```

---

# 2. Testing iOS Permission Dialog Prompts

iOS enforces strict permission prompts for Location, Camera, Push Notifications, and App Tracking Transparency (ATT).

```text
┌────────────────────────────────────────────────────────┐
│             iOS PERMISSION TESTING PROTOCOL            │
│                                                        │
│  1. Reset Simulator Privacy Permissions:               │
│     `xcrun simctl privacy booted reset all`            │
│  2. Launch App & Trigger Feature (e.g., Camera).       │
│  3. Assert `NSCameraUsageDescription` string renders.  │
│  4. Test 'Allow' and 'Don't Allow' User Paths.         │
└────────────────────────────────────────────────────────┘
```

---

# 3. Operational Verification Checklist

- [ ] **Simulator CLI Automated**: `xcrun simctl` commands integrated into local test scripts.
- [ ] **Permission Prompts Audited**: Usage strings rendered correctly for Camera, Location, and Notifications.
- [ ] **Denied State Handled**: App displays graceful fallback UI when user denies permissions.
- [ ] **TestFlight Sandbox Validated**: Build tested on TestFlight environment before store submission.

---

# 4. Official Sources

- Apple Xcode Simulator Help: https://developer.apple.com/documentation/xcode/running-apps-in-the-simulator

---

**Last verified:** August 14, 2026
