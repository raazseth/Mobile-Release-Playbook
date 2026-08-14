# Phased & Staged Rollout Monitoring Handbook

This handbook details rollout monitoring protocols, iOS 7-Day Phased Release schedules, Google Play Staged Rollout percentages (1% -> 100%), threshold alert triggers, and halting criteria for **Rollout Monitoring** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to execute progressive releases safely.

This guide is **not**:

- an authorization mechanism to jump from 1% to 100% rollout without monitoring crash telemetry
- a substitute for establishing automated alert thresholds
- a guide to ignoring early crash signals during Day 1 rollouts

---

# 1. Progressive Rollout Architecture & Schedules

Progressive rollouts limit user exposure to newly released code, allowing engineering teams to catch production defects early.

```text
┌────────────────────────────────────────────────────────┐
│             iOS 7-DAY PHASED RELEASE SCHEDULE          │
│                                                        │
│  - Day 1: 1%   │ Day 2: 2%   │ Day 3: 5%               │
│  - Day 4: 10%  │ Day 5: 20%  │ Day 6: 50%              │
│  - Day 7: 100% Automatic Full Release                  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             ANDROID STAGED ROLLOUT SCHEDULE            │
│                                                        │
│  - Stage 1: 1% ──→ Stage 2: 10% ──→ Stage 3: 50% ──→ 100%│
│  - Manual progression based on stable telemetry        │
└────────────────────────────────────────────────────────┘
```

---

# 2. Automated Halting Criteria & Thresholds

Progressive rollouts MUST be halted immediately if production telemetry breaches any of the following boundaries:

- **Crash-Free User Rate**: Drops below **99.5%**.
- **Android Vitals ANR Rate**: Exceeds **0.47%**.
- **Sentry Crash Spike**: > 100 crashes per 1,000 active sessions on new release version.

---

# 3. Operational Verification Checklist

- [ ] **Rollout Schedule Configured**: iOS 7-Day Phased Release active; Android Staged Rollout initiated at 1%.
- [ ] **Real-Time Telemetry Active**: Sentry and Crashlytics filtered by new release version.
- [ ] **Halting Procedure Ready**: Release manager authorized to halt rollout in App Store Connect / Play Console.

---

# 4. Official Sources

- Apple Phased Release Guide: https://developer.apple.com/help/app-store-connect/#/dev3b92cdd7c
- Google Play Staged Rollout Guide: https://support.google.com/googleplay/android-developer/answer/9859348

---

**Last verified:** August 14, 2026
