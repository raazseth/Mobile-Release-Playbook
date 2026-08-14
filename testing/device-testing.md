# Physical Device QA Protocol & Hardware Sensor Testing

This document details on-device QA protocols, physical hardware sensor testing (Camera, Biometrics, Geolocation, Bluetooth, Accelerometer), and thermal throttling checks for **Physical Device Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to execute physical device testing for features that cannot be evaluated accurately on simulators.

This guide is **not**:

- an authorization mechanism to mock hardware APIs exclusively
- a substitute for physical device QA before store submission
- a guide to skipping biometric Face ID / Touch ID testing

---

# 1. On-Device Hardware Sensor Testing Protocols

Hardware features rely on native device sensors, hardware permission grants, and physical interaction.

```text
┌────────────────────────────────────────────────────────┐
│             ON-DEVICE HARDWARE QA PROTOCOL             │
│                                                        │
│  - Biometrics: Face ID / Touch ID / Fingerprint Auth   │
│  - Camera & Gallery: Photo capture, QR scanning        │
│  - Geolocation: GPS tracking, background location      │
│  - Bluetooth / IoT: Peripheral discovery & pairing     │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified on Physical Hardware Run under Real-World Conditions ]
```

---

# 2. Key Sensor Test Matrix

| Hardware Feature | Physical Device Test Method | Key Failure Modes to Inspect |
|---|---|---|
| **Face ID / Biometrics** | Authenticate via physical device Face ID sensor | Fallback to passcode prompt on 3 failed attempts. |
| **Camera Capture** | Take live photo; scan QR code | Memory crash when capturing high-res 4K photo. |
| **GPS Geolocation** | Walk outdoors with active GPS session | Battery drain, background location permission revocation. |
| **Bluetooth (BLE)** | Pair with physical BLE peripheral | Connection timeout, Bluetooth disabled state handling. |

---

# 3. Operational Verification Checklist

- [ ] **Physical Hardware Deployment**: Test build installed on physical iOS and Android hardware.
- [ ] **Biometrics Verified**: Face ID / Touch ID / Fingerprint authentication tested on hardware.
- [ ] **Camera Flow Passed**: Live camera photo capture and gallery picker verified on device.
- [ ] **Thermal / Battery Checked**: Device tested for excessive thermal heating during 30-minute test session.

---

# 4. Official Sources

- Apple Hardware Integration: https://developer.apple.com/documentation/

---

**Last verified:** August 14, 2026
