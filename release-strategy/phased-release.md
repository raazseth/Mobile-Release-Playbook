# iOS 7-Day Phased Release Strategy Handbook

This handbook details App Store Connect 7-day automatic Phased Release schedules, daily adoption percentage calculations, manual overrides, halting criteria, and monitoring protocols for **iOS Phased Release** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to manage iOS progressive rollouts.

This guide is **not**:

- an authorization mechanism to bypass iOS Phased Release for standard production updates
- a substitute for monitoring Sentry crash rates during the 7-day rollout window
- a guide to resuming a halted phased release without fixing root cause issues

---

# 1. App Store Connect 7-Day Phased Release Schedule

When enabled in App Store Connect, iOS Phased Release distributes updates automatically to a random percentage of users with automatic updates enabled over 7 days:

```text
┌────────────────────────────────────────────────────────┐
│             iOS 7-DAY AUTOMATIC ROLLOUT SCHEDULE       │
│                                                        │
│  - Day 1:  1%  of automatic update user population     │
│  - Day 2:  2%  of automatic update user population     │
│  - Day 3:  5%  of automatic update user population     │
│  - Day 4: 10%  of automatic update user population     │
│  - Day 5: 20%  of automatic update user population     │
│  - Day 6: 50%  of automatic update user population     │
│  - Day 7: 100% of automatic update user population     │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Operational Controls & Halting Rules

- **Pause Phased Release**: Phased Release can be paused in App Store Connect for up to **30 days** at any point during the 7-day schedule if a critical bug is detected.
- **Release All Users**: Release Engineers can click "Release to All Users" to accelerate rollout to 100% immediately if telemetry is 100% clean.

---

# 3. Operational Verification Checklist

- [ ] **Phased Release Selected**: "Release update over 7-day period using phased release" option enabled in App Store Connect.
- [ ] **Telemetry Clean**: Crash-free rate > 99.5% verified on Day 1 (1%) and Day 4 (10%).
- [ ] **Pause Trigger Operational**: Team members trained on clicking "Pause Phased Release" in App Store Connect.

---

# 4. Official Sources

- Apple App Store Connect Phased Release Guidance: https://developer.apple.com/help/app-store-connect/release-an-app-update/phased-release

---

**Last verified:** August 14, 2026
