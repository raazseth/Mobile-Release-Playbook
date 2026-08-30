# Mobile Performance Profiling & Memory Leak Detection

This document details Hermes JS memory profiling, React Native Performance Monitor, FPS frame rate tracking, Time to Interactive (TTI), memory leak detection, and Android Vitals metrics for **Performance Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to measure, profile, and optimize mobile application performance.

This guide is **not**:

- an authorization mechanism to ignore 60 FPS / 120 FPS frame drops during scroll interactions
- a substitute for profiling Hermes heap allocation in production release builds
- a guide to over-rendering heavy React component subtrees

---

# 1. Performance Profiling Architecture & Hermes Heap Profiler

Performance profiling measures JS thread execution time, native UI frame rates, and memory allocation to maintain 60 FPS (or 120 FPS on ProMotion screens).

```text
┌────────────────────────────────────────────────────────┐
│             HERMES MEMORY PROFILING PIPELINE           │
│                                                        │
│  [ Chrome DevTools / Hermes CPU & Heap Sampling ]      │
│                         │                              │
│                         ▼                              │
│  - Inspect JS Thread Execution Time (Keep < 16.6ms)    │
│  - Measure Hermes Garbage Collection (GC) Pauses       │
│  - Identify Memory Leaks (Retained Objects Heap)      │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Stable 60 FPS Scrolling & Fast Time To Interactive ]
```

---

# 2. Key Performance Metric Targets

| Metric | Target Threshold | Key Optimization Technique |
|---|---|---|
| **UI Frame Rate** | **60 FPS** (120 FPS ProMotion) | Use `useNativeDriver: true` or Reanimated 3 for UI animations. |
| **JS Thread Frame Rate** | **60 FPS** (Max 16.6ms per frame) | Avoid heavy synchronous JS computations on main JS thread. |
| **Time to Interactive (TTI)**| **< 1.5 seconds** | Code split bundle; defer non-critical initialization. |
| **Hermes Memory Heap** | Stable without leaks | Unsubscribe event listeners in `useEffect` cleanup handlers. |
| **Android ANR Rate** | **< 0.47%** (Google Play Vitals) | Keep native main looper free of blocking I/O calls. |

---

# 3. Operational Verification Checklist

- [ ] **60 FPS Scrolling Verified**: FlatList and ScrollView components maintain 60 FPS scrolling.
- [ ] **No Memory Leaks**: Memory allocation remains stable after 20 minutes of continuous app usage.
- [ ] **Unmount Cleanup Verified**: `useEffect` cleanup functions remove all active event listeners.
- [ ] **Android Vitals Clean**: User-perceived ANR rate < 0.47% and crash rate < 1.09% in Play Console.

---

# 4. Official Sources

- React Native Performance Guide: https://reactnative.dev/docs/performance
- Hermes Engine Performance: https://hermesengine.dev/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Testing

- `testing/README.md`
- `testing/accessibility.md`
- `testing/android.md`
- `testing/deep-links.md`
- `testing/device-matrix.md`
- `testing/device-testing.md`
- `testing/e2e.md`
- `testing/integration.md`
- `testing/ios.md`
- `testing/localization.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/payments.md`
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
