# Physical Device Test Matrix Planning

This document details device selection criteria, OS version coverage, screen density tiers, CPU/GPU architecture splits, and memory constraint tiers for **Physical Device Matrix Planning** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to select a representative device test matrix for pre-release QA.

This guide is **not**:

- an authorization mechanism to limit testing to a single high-end flagship device
- a substitute for testing on low-memory budget Android hardware
- a guide to ignoring tablet display viewports

---

# 1. Physical Device Test Matrix Selection Taxonomy

To ensure broad hardware compatibility, testing MUST cover low-end, mid-range, and high-end flagship devices across iOS and Android.

```text
┌────────────────────────────────────────────────────────┐
│             PHYSICAL DEVICE TEST MATRIX                │
│                                                        │
│  - High-End Flagship: iPhone 16 Pro Max (iOS 18)       │
│  - Mainstream Mid-Tier: Pixel 8 (Android 15)           │
│  - Low-End Budget Tier: Galaxy A14 (2GB RAM / API 28)  │
│  - Tablet Viewport: iPad Pro 13" / Galaxy Tab S9       │
└────────────────────────────────────────────────────────┘
```

| Device Classification | Model Representative | OS Version | Hardware Scope | QA Focus Areas |
|---|---|---|---|---|
| **iOS Flagship** | iPhone 16 Pro Max | iOS 18+ | 6.9" Super Retina XDR, Dynamic Island | High-FPS animations, 120Hz ProMotion layout. |
| **iOS Legacy** | iPhone 11 / SE 3 | iOS 16 / 17 | 4.7" / 6.1" LCD, Home button | Small viewport layout clipping, battery drain. |
| **Android Flagship** | Google Pixel 9 Pro | Android 16 (API 36) | 12GB RAM, Tensor G4 | Target API 36 features, predictive back. |
| **Android Budget** | Samsung Galaxy A14 | Android 12 (API 31) | 2GB RAM, Mali GPU | Memory leaks, Hermes GC pauses, startup TTI. |
| **Tablet Class** | iPad Pro 13" (M4) | iPadOS 18+ | 2064x2752 px, Multi-window | Split-screen multitasking, landscape layout. |

---

# 2. Operational Verification Checklist

- [ ] **Budget Android Device Tested**: Low-memory Android device (≤ 3GB RAM) included in QA pass.
- [ ] **Dynamic Island / Notch Tested**: UI layout verified on notched and Dynamic Island iPhone models.
- [ ] **Tablet Viewport Verified**: Layout tested on 13" iPad Pro in both portrait and landscape.
- [ ] **OS Range Covered**: iOS 16–18 and Android 11–16 covered across physical test matrix.

---

# 3. Official Sources

- Android Device Screen Densities: https://developer.android.com/training/multiscreen/screensizes

---

**Last verified:** August 14, 2026

---

# Related documentation

### Testing

- `testing/README.md`
- `testing/accessibility.md`
- `testing/android.md`
- `testing/deep-links.md`
- `testing/device-testing.md`
- `testing/e2e.md`
- `testing/integration.md`
- `testing/ios.md`
- `testing/localization.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/payments.md`
- `testing/performance.md`
- `testing/push-notifications.md`
- `testing/release-smoke-tests.md`
- `testing/unit.md`
- `testing/upgrade-migrations.md`

### Pre-release

- `pre-release/README.md`
- `pre-release/final-release-checklist.md`

### Checklists

- `checklists/pre-release.md`

### Troubleshooting

- `troubleshooting/README.md`

### Release strategy

- `release-strategy/beta-testing.md`
