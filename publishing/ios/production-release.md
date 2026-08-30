# App Store Production Release & Phased Release Protocols

This document covers production release options, the Phased Release Over 7 Days schedule, pausing a rollout, release triggers, and crash monitoring for **App Store Production Releases** in Expo and React Native applications — how to release iOS updates to global users without exposing the entire user base to an unforeseen production crash at once.

This guide is **not**:

- an authorization mechanism to release un-tested builds directly to 100% of users
- a substitute for monitoring crash rates in Sentry during rollouts
- a guide to bypassing App Store Review approval

---

# 1. App Store Production Release Triggers

When submitting an app version for App Store Review, select one of three production release execution methods:

```text
Production release triggers (pick one when submitting a version)
  - Manually release this version   → release manager clicks "Release App" after approval (recommended)
  - Automatically release           → releases immediately on App Review approval
  - Automatically release on a date → releases at a specified date/time after approval
```

---

# 2. Phased Release Over 7 Days Schedule

Apple's **Phased Release** mechanism deploys a version update to users who have automatic updates enabled over a 7-day period according to a fixed percentage schedule.

```text
Phased release, 7-day schedule
  Day 1:  1% of active users
  Day 2:  2%
  Day 3:  5%
  Day 4: 10%
  Day 5: 20%
  Day 6: 50%
  Day 7: 100%
        │
        ├─→ crash rate stable    → proceed through the schedule
        └─→ crash spike detected → pause phased release (up to 30 cumulative days)
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

# Related documentation

### Publishing (iOS)

- `publishing/ios/README.md`
- `publishing/ios/app-review.md`
- `publishing/ios/app-store-connect.md`
- `publishing/ios/build-upload.md`
- `publishing/ios/metadata.md`
- `publishing/ios/screenshots.md`
- `publishing/ios/testflight.md`

### Store accounts

- `store-accounts/app-store-connect.md`

### iOS signing

- `signing/ios/README.md`
- `signing/ios/distribution.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/ios.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Apple Phased Release Help: https://developer.apple.com/help/app-store-connect/#/dev3e605e495

---

**Last verified:** August 14, 2026

