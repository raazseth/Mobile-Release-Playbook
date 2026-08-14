# Live Production Performance Tracking & Android Vitals

This handbook details live performance tracking, Android Vitals ANR thresholds (< 0.47%), app launch latency monitoring, API network request latency, and Hermes memory heap metrics for **Performance Tracking** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to monitor production performance to prevent store discoverability penalties.

This guide is **not**:

- an authorization mechanism to ignore Google Play Android Vitals ANR warnings
- a substitute for measuring real-world production TTI
- a guide to ignoring slow 3G network latency spikes

---

# 1. Android Vitals & Production APM Benchmarks

Google Play algorithms lower store discoverability rankings for apps exceeding Android Vitals bad behavior thresholds.

```text
┌────────────────────────────────────────────────────────┐
│             ANDROID VITALS BAD BEHAVIOR THRESHOLDS     │
│                                                        │
│  - User-Perceived ANR Rate: MUST be < 0.47%            │
│  - User-Perceived Crash Rate: MUST be < 1.09%          │
│  - Slow Cold Start Launch (> 5s): MUST be < 3.8%       │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Clean Vitals Status in Google Play Console ]
```

---

# 2. Key APM Monitoring Targets

- **Cold Start Latency**: App launch time to interactive home dashboard < 1.5 seconds.
- **API Network Latency**: P95 backend API network response time < 500 ms.
- **UI Frame Drops**: Frozen frames rate < 0.1% during screen transitions.

---

# 3. Operational Verification Checklist

- [ ] **Play Vitals Clean**: ANR rate < 0.47% and crash rate < 1.09% in Play Console.
- [ ] **APM Tracing Active**: Datadog APM / Firebase Performance Monitoring active.
- [ ] **Launch Time Monitored**: P95 cold start launch latency verified < 1.5 seconds.

---

# 4. Official Sources

- Android Vitals Core Metrics: https://developer.android.com/topic/performance/vitals

---

**Last verified:** August 14, 2026
