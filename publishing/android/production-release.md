# Android Production Track & Staged Rollout Administration

This document covers production track publishing, staged rollout percentages, halting a rollout, and crash monitoring for **Android Production Releases** in Expo and React Native applications — how to deploy a production update without exposing the entire user base to an unexpected crash at once.

This guide is **not**:

- an authorization mechanism to release un-tested builds directly to 100% of users
- a substitute for monitoring crash rates in Sentry or Firebase Crashlytics during rollouts
- a guide to bypassing production review requirements

---

# 1. Staged Rollout Architecture & Percentage Lifecycle

Google Play Staged Rollouts allow developers to release a production update to a fractional percentage of users, monitoring stability metrics before expanding the release globally.

```text
Staged rollout lifecycle
  Stage 1:  1% or 5%  → initial health check
  Stage 2: 10% or 20% → scale monitoring
  Stage 3: 50%        → final stability check
  Stage 4: 100%       → full release
        │
        ├─→ crash rate stable    → increase percentage
        └─→ crash spike detected → halt rollout immediately
```

---

# 2. Recommended Staged Rollout Schedule & Health Gates

| Rollout Stage | Target User Percentage | Minimum Evaluation Window | Rollout Health Metrics to Inspect |
|---|---|---|---|
| **Stage 1** | **1% to 5%** | 24 Hours | Below Google Play's published Android Vitals bad-behavior thresholds (ANR rate 0.47%, crash rate 1.09% — see Official Sources), zero critical payment crashes. |
| **Stage 2** | **10% to 20%** | 24 Hours | Crash-free session rate stable in your crash reporter (Crashlytics/Sentry); no new error spikes. |
| **Stage 3** | **50%** | 24 Hours | Play Console Android Vitals within acceptable thresholds. |
| **Stage 4** | **100%** | Final Release | Complete rollout; close release ticket. |

Google Play's Android Vitals bad-behavior thresholds are account-wide quality bars, not staged-rollout-specific gates — Google doesn't publish separate thresholds per rollout stage. Using them as a stage-1 checkpoint is this playbook's own heuristic, not an official Google requirement.

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

# Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/app-review.md`
- `publishing/android/closed-testing.md`
- `publishing/android/internal-testing.md`
- `publishing/android/metadata.md`
- `publishing/android/play-console.md`
- `publishing/android/screenshots.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Android signing

- `signing/android/README.md`
- `signing/android/play-app-signing.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Google Play Release Staged Rollout Guide: https://support.google.com/googleplay/android-developer/answer/6346149
- Android Vitals Thresholds: https://developer.android.com/topic/performance/vitals
- Android Vitals bad-behavior thresholds (crash/ANR rate): https://support.google.com/googleplay/android-developer/answer/9844486

---

**Last verified:** August 14, 2026

