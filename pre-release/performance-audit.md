# Application Performance Audit Handbook

This handbook details application performance audits, Hermes JS memory heap profiling, 60/120 FPS scrolling benchmarks, Time to Interactive (TTI < 1.5s), and Android Vitals ANR thresholds (< 0.47%) for **Performance Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit mobile app performance before approving a release candidate.

This guide is **not**:

- an authorization mechanism to release apps experiencing frame drops or high ANR rates
- a substitute for profiling Hermes JS heap memory
- a guide to ignoring budget Android device performance

---

# 1. Performance Audit Architecture

Performance auditing benchmarks UI frame rates, JS thread execution time, cold start TTI, and memory consumption.

```text
┌────────────────────────────────────────────────────────┐
│             PERFORMANCE AUDIT METRICS                  │
│                                                        │
│  - UI & JS Frame Rate: 60 FPS (120 FPS ProMotion)     │
│  - Time to Interactive (TTI): < 1.5 Seconds            │
│  - Hermes Heap Memory: Stable without leaks           │
│  - Android Vitals ANR Rate: < 0.47%                   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified High-Performance User Experience Across Matrix ]
```

---

# 2. Key Performance Benchmark Targets

- **UI & JS Thread Frame Rate**: 60 FPS minimum (120 FPS on ProMotion screens) during FlatList scrolling.
- **Cold Start TTI**: App boots to interactive home dashboard in < 1.5 seconds.
- **Android Vitals**: User-perceived ANR rate strictly < 0.47% and crash rate strictly < 1.09%.

---

# 3. Operational Verification Checklist

- [ ] **60 FPS Scrolling Verified**: Smooth 60 FPS scrolling verified on low-end budget device.
- [ ] **TTI Measured**: Cold start Time to Interactive verified < 1.5s.
- [ ] **No Memory Leaks**: Heap memory remains stable after 20 minutes of continuous usage.

---

# 4. Official Sources

- Android Vitals Core Metrics: https://developer.android.com/topic/performance/vitals

---

**Last verified:** August 14, 2026

---

# Related documentation

### Pre-release

- `pre-release/README.md`
- `pre-release/accessibility-audit.md`
- `pre-release/asset-audit.md`
- `pre-release/configuration-audit.md`
- `pre-release/dependency-audit.md`
- `pre-release/final-release-checklist.md`
- `pre-release/metadata-audit.md`
- `pre-release/permissions-audit.md`
- `pre-release/privacy-audit.md`
- `pre-release/release-readiness.md`
- `pre-release/security-audit.md`

### Testing

- `testing/release-smoke-tests.md`
- `testing/README.md`

### Checklists

- `checklists/pre-release.md`
- `checklists/production.md`

### Store operations

- `store-operations/app-review.md`

### Privacy compliance

- `privacy-compliance/README.md`

### AI agents

- `ai/agents/release-auditor.md`
- `ai/agents/security-auditor.md`
- `ai/agents/privacy-auditor.md`

### Publishing

- `publishing/ios/README.md`
- `publishing/android/README.md`
