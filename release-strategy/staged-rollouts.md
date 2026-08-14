# Android Google Play Staged Rollout Strategy Handbook

This handbook details Google Play Console Staged Rollout percentages (1%, 5%, 10%, 20%, 50%, 100%), Android Vitals quality gate monitoring (ANR < 0.47%, Crash rate < 1.09%), halting rollouts, and Fastlane Supply automation for **Android Staged Rollouts**.

Engineered in alignment with **2026 platform specifications**, it specifies how to execute progressive rollouts on Android.

This guide is **not**:

- an authorization mechanism to jump from 1% to 100% rollout without checking Android Vitals
- a substitute for halting rollouts when ANR bad behavior thresholds are breached
- a guide to creating new releases while a previous staged rollout is active without updating version codes

---

# 1. Google Play Staged Rollout Escalation Pipeline

Android Staged Rollouts allow custom percentage targets, enabling teams to expand distribution gradually as production metrics confirm stability.

```text
┌────────────────────────────────────────────────────────┐
│             ANDROID STAGED ROLLOUT ESCALATION          │
│                                                        │
│  [ Stage 1: 1% Rollout ]  ──→ Monitor Android Vitals 24h│
│             │                                          │
│             ▼                                          │
│  [ Stage 2: 10% Rollout ] ──→ Monitor Android Vitals 24h│
│             │                                          │
│             ▼                                          │
│  [ Stage 3: 50% Rollout ] ──→ Monitor Android Vitals 24h│
│             │                                          │
│             ▼                                          │
│  [ Stage 4: 100% Full Production Rollout ]             │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Fastlane Supply Automation Command

```ruby
# Promote staged rollout to 20% target percentage
upload_to_play_store(
  track: "production",
  rollout: "0.20",
  aab: "android/app/build/outputs/bundle/release/app-release.aab"
)
```

---

# 3. Operational Verification Checklist

- [ ] **Android Vitals Checked**: ANR rate < 0.47% and crash rate < 1.09% verified in Play Console.
- [ ] **1% Initial Rollout Set**: Initial production rollout launched at 1% target.
- [ ] **Halt Capability Verified**: Rollout halting tested via Play Console / Fastlane API.

---

# 4. Official Sources

- Google Play Console Staged Rollouts Help: https://support.google.com/googleplay/android-developer/answer/6346149

---

**Last verified:** August 14, 2026
