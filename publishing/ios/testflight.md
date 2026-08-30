# TestFlight Beta Testing Tracks & Distribution

This document covers internal testing tracks (up to 100 testers, no review delay), external testing tracks (up to 10,000 testers, Beta App Review required), public testing links, crash feedback collection, and build expiration for **TestFlight Beta Distribution** in Expo and React Native applications — how to distribute beta builds to internal QA teams and external beta testers.

This guide is **not**:

- an authorization mechanism to distribute un-reviewed external beta builds (external testing requires Beta App Review)
- a substitute for collecting crash reports via Sentry or Crashlytics
- a permanent distribution channel (TestFlight builds expire automatically after 90 days)

---

# 1. TestFlight Testing Track Architecture

TestFlight provides two distinct beta testing channels with different review requirements and tester capacity limits.

```text
TestFlight beta tracks
  ├─→ Internal testing track
  │     - up to 100 testers, assigned App Store Connect team roles
  │     - instant availability, no App Review delay
  └─→ External testing track
        - up to 10,000 testers, accessible via email or a public link
        - requires Beta App Review by Apple
```

---

# 2. Internal Testing Track Setup (100 Testers, Instant Access)

Internal Testing allows distributing builds instantly to team members added to your App Store Connect account:

1. **Add Internal Testers**: Users must be invited to App Store Connect under Users and Access with a role (e.g., Developer, App Manager, Admin).
2. **Assign to Internal Group**: Add testers to an Internal Testing Group in TestFlight.
3. **Instant Access**: As soon as a build finishes processing and passes export compliance, internal testers receive an instant push notification from the TestFlight iOS app.

---

# 3. External Testing Track Setup (10,000 Testers, Beta Review)

External Testing opens beta builds to external users via email invitation or a Public Link:

```text
Upload build to TestFlight
        ↓
Add build to external testing group, submit for Beta App Review
        ↓
Beta App Review (Apple evaluates build stability & compliance; typically 24-48 hours)
        ↓ (approved)
Distribute to external testers via email or public link
```

> **NOTE ON BUILD UPDATES**: Once an external group is approved for a marketing version (e.g., `1.2.0`), subsequent build updates for the same marketing version (e.g., build `1.2.0.2`) usually do NOT require full Beta App Review.

---

# 4. Build Expiration & TestFlight Crash Feedback

- **90-Day Expiration**: TestFlight builds remain active for **90 days** from the date of upload. After 90 days, the build expires and cannot be launched.
- **Native Crash Feedback**: TestFlight automatically captures crashes and screenshot feedback submitted by testers, accessible in App Store Connect under TestFlight -> Crash Feedback.

---

# 5. Operational Verification Checklist

- [ ] **Internal Group Assigned**: Internal testers assigned to internal group for instant delivery.
- [ ] **Beta Review Submitted**: First build for new marketing version submitted for Beta App Review.
- [ ] **Public Link Managed**: Public link tester limit capped (e.g., 500 max) to prevent unauthorized distribution.
- [ ] **TestFlight Feedback Monitored**: Crash logs and screenshot feedback reviewed in App Store Connect.
- [ ] **Expiration Dates Tracked**: 90-day expiration window monitored for active beta builds.

---

# Related documentation

### Publishing (iOS)

- `publishing/ios/README.md`
- `publishing/ios/app-review.md`
- `publishing/ios/app-store-connect.md`
- `publishing/ios/build-upload.md`
- `publishing/ios/metadata.md`
- `publishing/ios/production-release.md`
- `publishing/ios/screenshots.md`

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

- TestFlight Overview & Help: https://developer.apple.com/testflight/

---

**Last verified:** August 14, 2026

