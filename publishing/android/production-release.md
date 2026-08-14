# Android Production Track & Staged Rollout Administration

This document details the production track publishing, Staged Rollout percentage increments (1%, 5%, 10%, 20%, 50%, 100%), rollout halting protocols, emergency rollbacks, and crash monitoring for **Android Production Releases** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to deploy production updates safely to millions of Android devices without exposing the entire user base to unexpected production crashes.

This guide is **not**:

- an authorization mechanism to release un-tested builds directly to 100% of users
- a substitute for monitoring crash rates in Sentry or Firebase Crashlytics during rollouts
- a guide to bypassing production review requirements

---

# 1. Staged Rollout Architecture & Percentage Lifecycle

Google Play Staged Rollouts allow developers to release a production update to a fractional percentage of users, monitoring stability metrics before expanding the release globally.

```text
┌────────────────────────────────────────────────────────┐
│             STAGED ROLLOUT PERCENTAGE LIFECYCLE        │
│                                                        │
│  Stage 1:  1% or 5% Rollout  ──→ Initial Health Check │
│  Stage 2: 10% or 20% Rollout ──→ Scale Monitoring     │
│  Stage 3: 50% Rollout        ──→ Final Stability Check│
│  Stage 4: 100% Rollout       ──→ Full Global Release  │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
    [ Crash Rate Stable ]      [ Crash Spike Detected ]
    Increase Percentage        HALT ROLLOUT IMMEDIATELY
```

---

# 2. Recommended Staged Rollout Schedule & Health Gates

| Rollout Stage | Target User Percentage | Minimum Evaluation Window | Rollout Health Metrics to Inspect |
|---|---|---|---|
| **Stage 1** | **1% to 5%** | 24 Hours | ANR rate < 0.47%, Crash rate < 1.09%, zero critical payment crashes. |
| **Stage 2** | **10% to 20%** | 24 Hours | Firebase Crashlytics crash-free users > 99.2%, Sentry error count stable. |
| **Stage 3** | **50%** | 24 Hours | Play Console Android Vitals within acceptable thresholds. |
| **Stage 4** | **100%** | Final Release | Complete rollout; close release ticket. |

---

# 3. Halting a Staged Rollout (Emergency Response)

If a critical production bug, memory leak, or crash spike is detected during a staged rollout:

```text
Google Play Console ──→ [ Production Track → Edit Release ] ──→ [ Click "Halt Rollout" ]
```

- **Effect of Halting**: Halting a rollout prevents **new** users from receiving the updated build. Users who already updated will remain on the updated build until a replacement build with a higher build number is published.
- **Resuming vs Replacing**: Once a rollout is halted, you can fix the bug, increment `versionCode`, upload a new `.aab`, and start a new staged rollout at 1% or 5%.

---

# 4. Operational Verification Checklist

- [ ] **Staged Rollout Initialized**: Production release started at 1% or 5% initial rollout percentage.
- [ ] **Android Vitals Monitored**: User-perceived ANR rate (< 0.47%) and crash rate (< 1.09%) checked in Play Console.
- [ ] **Crashlytics Health Clear**: Crashlytics crash-free session metric > 99.2%.
- [ ] **Halt Protocol Ready**: Release manager trained on halting rollouts immediately if crash spike occurs.
- [ ] **100% Completion Approved**: Final expansion to 100% approved by Lead Release Engineer.

---

# 5. Related Documentation

- [App Bundle Handbook](app-bundle.md) - `.aab` compilation.
- [Play Console Handbook](play-console.md) - Service accounts.
- [Internal Testing Handbook](internal-testing.md) - Internal testing.

---

# 6. Official Sources

- Google Play Release Staged Rollout Guide: https://support.google.com/googleplay/android-developer/answer/6346149
- Android Vitals Thresholds: https://developer.android.com/topic/performance/vitals

---

**Last verified:** August 14, 2026
