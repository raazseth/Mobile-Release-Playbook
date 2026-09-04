# App Store Production Release

Once App Store Review approves a build, you still choose how it reaches users — all at once or gradually. This covers release triggers and Apple's phased release mechanism, which is the recommended way to catch a bad update before it hits everyone.

This guide is **not**:

- an authorization mechanism to release an un-tested build straight to 100% of users
- a substitute for watching crash rates in Sentry (or your crash reporter of choice) during a rollout
- a guide to bypassing App Store Review approval

---

## 1. Choosing a release trigger

When you submit a version for review, you pick one of three ways it goes live afterward:

```text
Production release triggers (pick one when submitting a version)
  - Manually release this version   → you click "Release App" after approval (recommended)
  - Automatically release           → releases immediately on approval
  - Automatically release on a date → releases at a date/time you set, after approval
```

## 2. Phased release over 7 days

Apple's **Phased Release** rolls a version out to users with automatic updates enabled, over 7 days, at a fixed schedule:

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
        ├─→ crash rate stable    → the schedule proceeds automatically
        └─→ crash spike detected → pause it (up to 30 cumulative days)
```

> **Note:** A phased release only controls *automatic background updates*. Users can still go to your App Store page and download the new version manually at any point during the rollout.

## 3. Pausing a rollout

If you spot a crash spike or a backend failure mid-rollout:

1. In App Store Connect, open the version details and click **Pause Phased Release**.
2. You can keep it paused for up to **30 cumulative days**.
3. Fix the issue, bump the build number, upload a new build, and submit a new version — you'll start a fresh phased release at 1% or 5%.

## 4. Before you release

- [ ] Phased release is enabled for the production submission.
- [ ] You picked "Manually Release This Version" so you control exact timing.
- [ ] Crash-free rate is being watched daily during the 7-day window, not just checked once.
- [ ] Whoever's on call knows how to pause the rollout if things go sideways.
- [ ] Day 7 completion is confirmed before you close out the release.

---

## Official sources

- Apple Phased Release help: https://developer.apple.com/help/app-store-connect/#/dev3e605e495

**Last verified:** August 14, 2026

---

## Related documentation

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
