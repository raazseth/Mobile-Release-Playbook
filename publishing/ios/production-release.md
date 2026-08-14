# App Store Production Release & Phased Release Protocols

This document details the production release options, Phased Release Over 7 Days schedule, release halting protocols, automatic vs manual release triggers, and crash monitoring for **App Store Production Releases** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to safely release iOS updates to global users without exposing your entire user base to unforeseen production crashes.

This guide is **not**:

- an authorization mechanism to release un-tested builds directly to 100% of users
- a substitute for monitoring crash rates in Sentry during rollouts
- a guide to bypassing App Store Review approval

---

# 1. App Store Production Release Triggers

When submitting an app version for App Store Review, select one of three production release execution methods:

```text
                                PRODUCTION RELEASE TRIGGERS
                                             │
      ┌──────────────────────────────────────┼──────────────────────────────────────┐
      ▼                                      ▼                                      ▼
[ Manually Release This Version ]   [ Automatically Release ]             [ Automatically Release ]
Release Manager clicks "Release    App releases to store IMMEDIATELY     App releases automatically on a
App" in App Store Connect post     upon passing App Review approval.     specified date/time post approval.
approval (RECOMMENDED).
```

---

# 2. Phased Release Over 7 Days Schedule

Apple's **Phased Release** mechanism deploys a version update to users who have automatic updates enabled over a 7-day period according to a fixed percentage schedule.

```text
┌────────────────────────────────────────────────────────┐
│             PHASED RELEASE 7-DAY SCHEDULE              │
│                                                        │
│  - Day 1:  1% of active users                          │
│  - Day 2:  2% of active users                          │
│  - Day 3:  5% of active users                          │
│  - Day 4: 10% of active users                          │
│  - Day 5: 20% of active users                          │
│  - Day 6: 50% of active users                          │
│  - Day 7: 100% of active users                         │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
    [ Crash Rate Stable ]      [ Crash Spike Detected ]
    Proceed through Schedule   PAUSE PHASED RELEASE (Max 30 Days)
```

> **IMPORTANT NOTE**: Users can still manually download the update directly from your App Store product page at any time during a Phased Release. Phased Release only controls *automatic background updates*.

---

# 3. Pausing & Resuming a Phased Release

If a production crash spike or backend API failure occurs during a Phased Release:

1. **Pause Phased Release**: In App Store Connect under Version details -> Click **Pause Phased Release**.
2. **30-Day Pause Limit**: You can pause a Phased Release for up to **30 cumulative days**.
3. **Resubmit Fixed Build**: Compile a new build with an incremented build number (`buildNumber`), upload to App Store Connect, and submit a new version for review.

---

# 4. Operational Verification Checklist

- [ ] **Phased Release Enabled**: "Phased Release Over 7 Days" enabled for production version submission.
- [ ] **Manual Release Selected**: "Manually Release This Version" selected to control exact release timing.
- [ ] **Crashlytics / Sentry Monitored**: Crash-free user metric monitored daily during the 7-day rollout window.
- [ ] **Pause Protocol Ready**: Release manager trained on pausing Phased Release if crash spike occurs.
- [ ] **Day 7 Completion Verified**: 100% distribution verified on Day 7 of Phased Release.

---

# 5. Related Documentation

- [Build Upload Handbook](build-upload.md) - `.ipa` uploading.
- [App Store Connect Handbook](app-store-connect.md) - Versioning.
- [TestFlight Handbook](testflight.md) - Beta testing.

---

# 6. Official Sources

- Apple Phased Release Help: https://developer.apple.com/help/app-store-connect/#/dev3e605e495

---

**Last verified:** August 14, 2026
