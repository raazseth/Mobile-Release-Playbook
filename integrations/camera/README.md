# Camera & Media Integration

This guide details the integration architecture, native permission requirements, photo library access rules, and store review compliance for **Camera & Media Services** in React Native and Expo applications.

It specifies how to configure camera capture, barcode scanning, and media library access while ensuring compliance with Apple App Store Review Guidelines (Guideline 5.1.1) and Android 14+ Selected Photos Access policies.

This guide is **not**:

- an authorization mechanism to access camera hardware without user permission
- a generic image editor guide
- a substitute for declaring usage description strings in `Info.plist` and `AndroidManifest.xml`

---

# 1. Integration Architecture & Permission Lifecycle

Camera and media hardware access is protected by native OS permission gates. Applications must request access in-context when the user triggers a camera or photo picker feature.

```text
[ User Action: Tap "Take Profile Photo" ]
                   │
                   ▼
┌────────────────────────────────────────────────────────┐
│            NATIVE OS PERMISSION CHECK                  │
│  - Has user granted Camera / Photo Library permission? │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Permission Granted ]             [ Permission Prompt ]
 Open Native Camera Preview /        Prompt User with Custom
 Photo Picker Component              Usage Description String
```

---

# 2. iOS Usage Description Strings (`Info.plist`)

Every camera, microphone, or photo library key used in the binary must include a clear, human-readable usage description string in `Info.plist` (via Expo `app.json` plugins):

```json
{
  "expo": {
    "plugins": [
      [
        "expo-camera",
        {
          "cameraPermission": "FitnessTracker needs access to your camera to scan barcode nutrition labels."
        }
      ],
      [
        "expo-image-picker",
        {
          "photosPermission": "FitnessTracker needs access to your photo library to select a profile picture."
        }
      ]
    ]
  }
}
```

### Required Plist Keys

- **`NSCameraUsageDescription`**: Explains why the app accesses the physical camera.
- **`NSMicrophoneUsageDescription`**: Explains why the app records audio during video capture.
- **`NSPhotoLibraryUsageDescription`**: Explains why the app reads photos from the library.
- **`NSPhotoLibraryAddUsageDescription`**: Explains why the app saves captured photos to the library.

> **CRITICAL REJECTION RISK**: Missing any of these keys when the corresponding native framework is included in the binary will cause an immediate App Store Connect upload failure (`ITMS-90683`).

---

# 3. Android 14+ Selected Photos Access (`READ_MEDIA_VISUAL_USER_SELECTED`)

Starting with Android 14 (API Level 34), apps requesting access to photos must support the **Selected Photos Access** permission (`READ_MEDIA_VISUAL_USER_SELECTED`), allowing users to grant access to specific photos rather than their entire media library.

```xml
<!-- AndroidManifest.xml -->
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
<uses-permission android:name="android.permission.READ_MEDIA_VIDEO" />
<uses-permission android:name="android.permission.READ_MEDIA_VISUAL_USER_SELECTED" />
```

> **Best Practice**: Use system photo pickers (such as Expo Image Picker or Android System Photo Picker) whenever possible. System photo pickers do NOT require declaring native media permissions.

---

# 4. Operational Verification Checklist

- [ ] **Usage Descriptions Clear**: All `NSCameraUsageDescription` and `NSPhotoLibraryUsageDescription` strings clearly explain user value.
- [ ] **No Un-needed Microphones**: `NSMicrophoneUsageDescription` included ONLY if video recording with audio is enabled.
- [ ] **System Photo Picker Used**: Photo selection uses system photo pickers to eliminate full media library access permissions where feasible.
- [ ] **Android 14+ Compatible**: Selected Photos Access permission supported on Android 14+ (API Level 34+).
- [ ] **Denied State Handled**: App remains functional if user denies camera or photo permissions.

---

# 5. Related Documentation

- [Privacy Auditor Agent](../../ai/agents/privacy-auditor.md) - Privacy Auditor agent definition.
- [Release Preparation Workflow](../../ai/workflows/release-preparation.md) - Pre-release checks.
- [Foundations Device Support](../../foundations/device-support.md) - Device requirements.

---

# 6. Official Sources

- Apple Camera & Media Permissions: https://developer.apple.com/documentation/avfoundation/cameras_and_media_capture
- Android Media Permissions & Photo Picker: https://developer.android.com/training/data-storage/shared/photopicker

---

**Last verified:** August 13, 2026

