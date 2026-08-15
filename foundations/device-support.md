# Device Support

Device support defines **where the released app is expected to work**.

A mobile app does not support "all iPhones" or "all Android phones" simply because it builds successfully. Support depends on operating-system versions, device families, screen sizes, hardware capabilities, CPU architectures, memory, platform restrictions, and the application's own requirements.

The goal is not to test every device.

The goal is to define a support policy, understand the real device population, test the highest-risk combinations, and prevent unsupported devices from becoming accidental production targets.

This guide is framework-independent, with **Expo and React Native as first-class implementations**.

---

## 1. What device support means

Device support has several layers:

```text
Device
  ↓
Operating system
  ↓
Platform SDK
  ↓
Application binary
  ↓
Native dependencies
  ↓
Application features
```

A device can be technically compatible with the platform but still be unsuitable for your application.

For example:

```text
Android device
    +
Supported Android version
    +
Required CPU architecture
    +
Required hardware
    +
Supported screen size
    +
Compatible native dependencies
    ↓
Application supported
```

Device support is therefore a product and release decision, not only a build configuration.

---

## 2. Define a support policy

Before release, explicitly define:

- Minimum supported OS version
- Supported device families
- Supported screen sizes
- Supported architectures
- Required hardware
- Unsupported device categories
- Testing coverage
- Upgrade policy
- End-of-support policy

A simple policy might look like:

```text
iOS
Minimum OS: <project decision>
Supported families: iPhone + iPad
Architectures: <project decision>

Android
Minimum OS: <project decision>
Supported device classes: phones + tablets
Architectures: <project decision>
```

Do not copy another application's support policy.

Choose a minimum based on:

- current user/device distribution
- framework support
- dependency support
- platform requirements
- product requirements
- security requirements
- testing cost
- business impact

---

## 3. Minimum OS version

The minimum OS version determines the oldest operating system that the application binary is intended to support.

### iOS

The minimum iOS version is controlled by the application's deployment target.

Apple exposes the resulting minimum OS requirement in App Store Connect build metadata. Apple calculates the build's device requirements from the build settings and reports the minimum OS version for the uploaded build.

### Android

Android uses the application's minimum supported SDK level (`minSdk`).

The minimum supported Android version affects which devices can install or run the application.

Do not confuse:

```text
minSdk
```

with:

```text
targetSdk
```

or:

```text
compileSdk
```

They solve different problems.

---

## 4. Android API levels

Android support should be reasoned about using API levels rather than only marketing version names.

Important concepts:

```text
minSdk
→ oldest Android API level the app supports

targetSdk
→ Android API level the app is designed/tested against for platform behavior and Play requirements

compileSdk
→ SDK used to compile the application
```

A project can have:

```text
minSdk < targetSdk <= compileSdk
```

but the exact supported configuration depends on the framework, dependencies, and current platform requirements.

Google Play also has current target API requirements for submissions. Starting August 31, 2026, new apps and updates submitted to Google Play must target Android 16 (API level 36) or higher, subject to the platform-specific exceptions documented by Google.

Do not use the target API requirement as the application's minimum supported Android version.

---

## 5. iOS deployment target

For iOS, define the minimum supported iOS version intentionally.

For React Native projects this may be configured in the native iOS project.

For Expo projects, the deployment target can be configured through Expo app configuration/build properties where appropriate. Expo documents `ios.deploymentTarget` as the setting that defines the minimum iOS version supported by the application.

Do not increase the deployment target simply to remove an old-device test.

Increase it when there is a real reason, such as:

- framework requirement
- dependency requirement
- security requirement
- platform API requirement
- meaningful product decision

---

## 6. Framework support matters

Your application's support range is constrained by the framework and its dependencies.

For Expo:

```text
Expo SDK
    ↓
React Native version
    ↓
Supported iOS/Android versions
    ↓
Native dependencies
    ↓
Your app
```

Expo publishes the minimum Android and iOS versions supported by each SDK version. For example, the current Expo SDK reference documents the supported OS versions and compile/target SDK values for each SDK release.

Do not define a device-support policy independently of the framework version.

Check:

- Expo SDK support
- React Native support
- native library support
- build tooling support
- Apple requirements
- Google Play requirements

---

## 7. Device families

Decide which device families the application supports.

### iOS

Common categories:

- iPhone
- iPad

If the application supports iPad:

- [ ] iPad layout is intentionally supported.
- [ ] Navigation works at larger widths.
- [ ] Orientation behavior is correct.
- [ ] Split View behavior is tested where applicable.
- [ ] Larger text/layout behavior is tested.
- [ ] Keyboard behavior is tested.
- [ ] Store configuration reflects the intended device support.

Expo provides configuration for declaring whether an iOS standalone app supports tablet screen sizes.

Do not claim iPad support simply because the application technically launches on an iPad.

### Android

Android devices vary much more widely.

Consider:

- phones
- tablets
- foldables
- different manufacturers
- different screen densities
- different aspect ratios
- different hardware capabilities

Android's compatibility model allows many hardware manufacturers to produce compatible devices, so the application should be tested against meaningful device classes rather than one representative Android phone.

---

## 8. Screen sizes and layouts

Do not define device support only by named models.

Test layout behavior across:

- small phones
- normal phones
- large phones
- tablets
- unusual aspect ratios
- foldable layouts where supported

Verify:

- [ ] Content is not clipped.
- [ ] Important controls remain reachable.
- [ ] Text does not overlap.
- [ ] Keyboard does not hide required controls.
- [ ] Safe areas are respected.
- [ ] System bars do not cover content.
- [ ] Images scale correctly.
- [ ] Modals fit the screen.
- [ ] Bottom sheets behave correctly.
- [ ] Landscape mode works if supported.

Prefer responsive layouts over device-specific pixel assumptions.

---

## 9. Orientation

Define supported orientations explicitly.

Possible policies:

```text
Portrait only
Landscape only
Portrait + landscape
Platform-dependent
```

For each supported orientation:

- [ ] Layout works.
- [ ] Navigation works.
- [ ] Keyboard behavior works.
- [ ] Modals work.
- [ ] Media behaves correctly.
- [ ] Deep links still open the correct screen.
- [ ] State is preserved across rotation where expected.

Do not support an orientation accidentally.

If the product is portrait-only, configure and test it as portrait-only.

---

## 10. Device hardware requirements

Some applications depend on hardware.

Examples:

- Camera
- Microphone
- GPS
- Bluetooth
- NFC
- Biometrics
- Motion sensors
- Secure hardware
- Push notification support
- AR capabilities

For every required hardware feature:

- [ ] Determine whether the feature is mandatory or optional.
- [ ] Detect availability at runtime.
- [ ] Provide a safe fallback when practical.
- [ ] Handle denied permissions.
- [ ] Test devices without the hardware where they may still install.
- [ ] Do not assume hardware exists because the app is running on a supported OS.

If a feature is truly required for the application to function, make the requirement explicit rather than allowing incompatible devices to discover the limitation later.

---

## 11. CPU architectures

Native mobile applications must be built for the architectures they intend to support.

Architecture issues can appear after adding:

- native React Native libraries
- custom native modules
- prebuilt native SDKs
- binary frameworks
- older third-party libraries

Review:

- [ ] Required iOS architectures are supported.
- [ ] Required Android ABIs are supported.
- [ ] Native dependencies provide compatible binaries.
- [ ] Release builds use the intended architecture configuration.
- [ ] Architecture-specific runtime behavior has been tested where relevant.

Expo's device APIs can expose supported CPU architectures at runtime, which can also be useful when diagnosing device-specific native issues.

Do not add architecture-specific workarounds unless the actual release configuration requires them.

---

## 12. Memory and performance classes

Two devices with the same OS version can behave very differently.

Consider:

- RAM
- CPU performance
- GPU capability
- storage pressure
- network quality
- thermal behavior

Test at least one lower-end device when the Android audience includes lower-cost hardware.

Check:

- [ ] App does not crash under memory pressure.
- [ ] Large images are handled safely.
- [ ] Lists remain usable.
- [ ] Startup remains acceptable.
- [ ] Heavy screens do not freeze the UI.
- [ ] Background/foreground transitions are stable.
- [ ] Network retries do not create excessive work.

Do not define "supported" as "the app launches."

A device is practically supported only when the important user journeys work acceptably.

---

## 13. Android manufacturer differences

Android behavior can vary between manufacturers.

Potential differences include:

- background execution
- battery optimization
- notification behavior
- permissions
- camera behavior
- system UI
- WebView behavior
- keyboard behavior
- file handling
- vendor-specific Android modifications

Do not attempt to test every manufacturer.

Instead, identify the manufacturers and device classes that matter for your real user base.

Use production analytics to refine the matrix.

---

## 14. Device support matrix

Maintain a small, intentional test matrix.

Example:

| Category | iOS | Android |
|---|---|---|
| Minimum supported OS | [ ] | [ ] |
| Current OS | [ ] | [ ] |
| Older supported OS | [ ] | [ ] |
| Small screen | [ ] | [ ] |
| Large screen | [ ] | [ ] |
| Tablet | [ ] | [ ] |
| Lower-end hardware | N/A | [ ] |
| High-end hardware | [ ] | [ ] |
| Older device | [ ] | [ ] |
| New device | [ ] | [ ] |
| Main user devices | [ ] | [ ] |

The matrix should represent risk, not every device that exists.

---

## 15. Choose devices by risk

A good test matrix usually contains:

```text
Minimum supported OS
        +
Current OS
        +
Most-used device class
        +
Lowest practical hardware class
        +
Largest/smallest important screen
        +
High-risk native feature device
```

For Android, also consider:

```text
Top manufacturer/device family
        +
Low-memory device
        +
Tablet/foldable if relevant
```

The exact devices should come from the application's actual audience and feature set.

---

## 16. Use production data to refine support

Device support should evolve from evidence.

Useful signals include:

- active devices
- OS versions
- crash rates by device
- crash rates by OS
- performance by device class
- feature failures
- store availability
- support requests
- installation/update behavior

A useful loop is:

```text
Production data
      ↓
Device/OS distribution
      ↓
Identify high-risk combinations
      ↓
Update test matrix
      ↓
Test future releases
```

Do not expand the test matrix simply because a new device exists.

Expand it when the device or configuration represents meaningful user or release risk.

---

## 17. Supported vs tested vs compatible

These terms should not be treated as synonyms.

### Supported

The project intentionally promises that the application should work there.

### Tested

The project has actually validated the application on that configuration.

### Compatible

The platform or store considers the application technically installable or runnable.

A device can be:

```text
Compatible
but not
Supported
```

or:

```text
Supported
but not
individually tested
```

The distinction matters.

Do not claim that every supported device has been individually tested.

---

## 18. Store/device compatibility

Stores use application metadata and binary requirements to determine device availability.

For iOS, App Store Connect exposes device requirements for uploaded builds, including minimum OS, supported architectures, device family, and required capabilities.

For Android, Google Play uses compatibility information to determine which devices can access an application. Android's official compatibility documentation distinguishes platform/device compatibility from application compatibility.

If a device cannot see or install the application:

```text
Check store availability
        ↓
Check OS version
        ↓
Check device family
        ↓
Check architecture
        ↓
Check required hardware
        ↓
Check manifest/build configuration
        ↓
Check platform restrictions
```

Do not immediately assume the store is broken.

---

## 19. Accessibility and device support

Accessibility is part of device and release support.

Test:

- [ ] Dynamic text sizing.
- [ ] Screen readers.
- [ ] Touch target sizes.
- [ ] Keyboard navigation where applicable.
- [ ] High contrast/system appearance where applicable.
- [ ] Reduced motion where supported.
- [ ] Content remains usable with accessibility settings enabled.

A device should not be considered practically supported if normal accessibility settings make the core application unusable.

---

## 20. Network conditions

Device support also includes realistic network conditions.

Test:

- Wi-Fi
- mobile data
- slow connection
- intermittent connection
- offline state
- reconnect
- switching networks

Verify:

- [ ] Requests time out safely.
- [ ] Retry behavior is bounded.
- [ ] Duplicate actions are prevented.
- [ ] Offline state is understandable.
- [ ] Reconnect restores the expected state.
- [ ] Long requests do not freeze the UI.

Network behavior is especially important for lower-end devices and real-world mobile conditions.

---

## 21. OS upgrade behavior

Users may upgrade their operating system while keeping the same app.

Test important transitions where practical:

```text
Older supported OS
       ↓
OS upgrade
       ↓
Existing app
       ↓
App launch
       ↓
Core flow
```

Check:

- [ ] App still launches.
- [ ] Stored data remains usable.
- [ ] Permissions behave correctly.
- [ ] Notifications still work.
- [ ] Deep links still work.
- [ ] Authentication remains valid where expected.
- [ ] Native modules remain compatible.

Do not assume an OS upgrade is equivalent to a clean install.

---

## 22. App upgrade behavior

Test application upgrades across supported devices.

At minimum:

```text
Previous production version
        ↓
New production version
        ↓
Launch
        ↓
Existing user data
        ↓
Core flow
```

Verify:

- [ ] Database/storage migrations work.
- [ ] Authentication state behaves correctly.
- [ ] Cached data does not corrupt the application.
- [ ] Feature flags remain valid.
- [ ] Deep links work.
- [ ] Push tokens remain valid or refresh correctly.
- [ ] New native dependencies initialize correctly.

This is particularly important when changing native dependencies or storage schemas.

---

## 23. Expo-specific device support

Expo projects should track three related compatibility boundaries:

```text
Expo SDK
    ↓
React Native version
    ↓
Native platform requirements
```

Check the current Expo SDK reference before choosing a support range.

Expo publishes the supported Android and iOS versions for each SDK. The current SDK reference should be treated as the source of truth for Expo-level compatibility, while Apple and Google remain the authorities for store requirements.

For Expo projects:

- [ ] Expo SDK support range checked.
- [ ] React Native version checked.
- [ ] Native module support checked.
- [ ] `ios.deploymentTarget` reviewed where configured.
- [ ] Android minimum SDK reviewed where configured.
- [ ] Build properties reviewed where customized.
- [ ] Production build tested on the oldest supported OS.

Do not set a lower OS version than the framework and native dependencies actually support.

---

## 24. React Native-specific device support

React Native applications need to consider both JavaScript and native compatibility.

Review:

- React Native version
- native modules
- iOS deployment target
- Android minimum SDK
- Android target SDK
- native architecture
- Hermes/runtime configuration where applicable
- third-party native libraries

A dependency can reduce the real support range even if React Native itself supports an older OS.

Therefore:

```text
Real support range
=
Framework support
∩
Native dependency support
∩
Platform requirements
∩
Application requirements
```

The narrowest meaningful boundary wins.

---

## 25. Feature-specific device matrix

If the app has hardware-dependent features, create a small feature matrix.

Example:

| Feature | iOS | Android | Device requirement |
|---|---|---|---|
| Camera | [ ] | [ ] | Camera |
| Location | [ ] | [ ] | Location services |
| Push | [ ] | [ ] | Push support |
| Bluetooth | [ ] | [ ] | Bluetooth |
| Biometrics | [ ] | [ ] | Biometric hardware |
| NFC | [ ] | [ ] | NFC where required |
| Background processing | [ ] | [ ] | Platform-specific behavior |

Only include features the application actually uses.

---

## 26. When to drop device/OS support

Support should not be removed casually.

Consider dropping an OS/device when:

- framework support has ended
- a required dependency no longer supports it
- security requirements cannot be met
- platform requirements make support impractical
- usage has become very small
- testing cost is disproportionate
- important product functionality cannot work correctly

Before dropping support:

- [ ] Measure affected users.
- [ ] Identify affected devices.
- [ ] Check framework/dependency constraints.
- [ ] Check store requirements.
- [ ] Determine migration/upgrade behavior.
- [ ] Decide whether existing installations continue to work.
- [ ] Document the decision.
- [ ] Test the new minimum boundary.

Do not drop old devices simply because testing them is inconvenient.

---

## 27. Communicating support changes

If the minimum supported OS changes:

- [ ] Update release documentation.
- [ ] Update the support policy.
- [ ] Update QA/device matrix.
- [ ] Update store expectations.
- [ ] Determine how existing users are affected.
- [ ] Prepare user communication when the impact is significant.
- [ ] Verify the new build's actual device requirements.

Do not promise support for a version the next release cannot install on.

---

## 28. Device support troubleshooting

### App is not available for a device

Check:

```text
Device OS
→ Minimum OS
→ Device family
→ Architecture
→ Required hardware
→ Store availability
→ Build metadata
```

For iOS, inspect App Store Connect build metadata and device requirements.

For Android, inspect Play Console compatibility information and the application's manifest/device requirements.

---

### App installs but crashes on one device

Check:

- OS version
- device model
- architecture
- native dependency
- memory pressure
- permissions
- vendor-specific behavior
- crash logs

Compare:

```text
Working device
vs
Failing device
```

The difference is often more useful than the crash message alone.

---

### Layout breaks on one screen size

Check:

- fixed widths
- absolute positioning
- safe areas
- font scaling
- keyboard handling
- orientation
- dynamic content
- platform-specific UI behavior

Do not add a device-specific CSS/style hack before understanding the layout constraint that failed.

---

### Feature works on iOS but not Android

Check:

- native implementation
- permissions
- platform APIs
- dependency support
- configuration
- background behavior
- device hardware

Do not assume that a cross-platform JavaScript API guarantees identical native behavior.

---

## 29. AI-assisted device support review

AI can help analyze a repository and identify device-support risks.

Useful tasks:

- inspect minimum OS configuration
- compare iOS and Android support settings
- inspect Expo configuration
- inspect React Native native configuration
- identify native dependencies with platform constraints
- identify hardware permissions
- inspect orientation configuration
- inspect tablet support
- review device-test coverage
- generate a candidate device matrix
- analyze crash data grouped by device/OS
- identify likely compatibility regressions

A safe workflow:

```text
Repository + release data
        ↓
AI analysis
        ↓
Candidate compatibility risks
        ↓
Developer verifies
        ↓
Build
        ↓
Device testing
        ↓
Human approval
```

Do not let AI invent a supported device range.

The AI should derive candidates from:

- project configuration
- framework documentation
- dependency documentation
- official platform requirements
- actual production data

Treat AI findings as recommendations until verified.

---

## 30. Device-support release gate

Before production release:

- [ ] Minimum iOS version is defined.
- [ ] Minimum Android version/API level is defined.
- [ ] Supported device families are defined.
- [ ] Supported architectures are understood.
- [ ] Required hardware is understood.
- [ ] Expo/React Native compatibility is verified.
- [ ] Native dependencies are compatible.
- [ ] Device matrix is current.
- [ ] Oldest supported OS has been tested.
- [ ] Current OS has been tested.
- [ ] Important screen sizes have been tested.
- [ ] Important hardware-dependent features have been tested.
- [ ] Upgrade from the previous production version has been tested.
- [ ] Store/device compatibility has been checked.
- [ ] Known unsupported devices are documented where necessary.

---

# 31. Recommended device testing strategy

For a normal release:

```text
1. Minimum supported OS
        ↓
2. Current OS
        ↓
3. Main user device class
        ↓
4. Lower-end device
        ↓
5. Largest/smallest important screen
        ↓
6. Hardware-specific device
        ↓
7. Production install/upgrade
```

Do not turn this into a requirement to own dozens of physical devices.

Use a combination of:

- physical devices
- simulators
- emulators
- CI/device farms where justified
- production telemetry

The test strategy should match the application's risk and audience.

---

# 32. Definition of done

Device support is in a good state when:

```text
Support policy defined
        +
Minimum OS defined
        +
Device families defined
        +
Framework compatibility checked
        +
Native dependencies checked
        +
Risk-based device matrix defined
        +
Oldest supported OS tested
        +
Current OS tested
        +
Important hardware tested
        +
Production data monitored
        ↓
   RELEASE READY
```

The goal is not:

> Test every device.

The goal is:

> Know what you support, test the combinations that matter, and detect problems that production users reveal.

---

# Official sources

Use official documentation for current platform and framework support.

### Apple

- App Store Connect build metadata and device requirements: https://developer.apple.com/help/app-store-connect/manage-builds/view-builds-and-metadata/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- Apple Developer: https://developer.apple.com/

### Google / Android

- Android device compatibility: https://developer.android.com/guide/practices/compatibility
- Google Play target API requirements: https://developer.android.com/google/play/requirements/target-sdk
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

### Expo

- Expo SDK reference: https://docs.expo.dev/versions/latest/
- Expo app configuration: https://docs.expo.dev/versions/latest/config/app/
- Expo build properties: https://docs.expo.dev/versions/latest/sdk/build-properties/
- Expo FAQ: https://docs.expo.dev/faq/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started
- React Native releases/versioning: https://reactnative.dev/releases/versioning-policy

**Last verified:** August 11, 2026

Minimum OS versions, SDK support, device compatibility, store requirements, and framework support can change. Re-check the relevant official source before changing the application's support policy or releasing a new version.
