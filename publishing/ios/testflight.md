# TestFlight

TestFlight is how you get a build in front of testers before it goes to the App Store — internally to your team, or externally to real beta users. This covers both tracks, plus how crash feedback and build expiration work.

This guide is **not**:

- an authorization mechanism to distribute an un-reviewed build to external testers — external testing requires Beta App Review
- a substitute for collecting crash reports through Sentry or Crashlytics
- a permanent distribution channel — TestFlight builds expire automatically after 90 days

---

## 1. Two tracks, two sets of rules

```text
TestFlight beta tracks
  ├─→ Internal testing
  │     - up to 100 testers, assigned App Store Connect team roles
  │     - instant, no App Review delay
  └─→ External testing
        - up to 10,000 testers, invited by email or a public link
        - requires Beta App Review by Apple
```

## 2. Internal testing (100 testers, instant)

Internal testing distributes builds instantly to people already on your App Store Connect team:

1. Invite testers to App Store Connect under Users and Access, with a role like Developer, App Manager, or Admin.
2. Add them to an Internal Testing Group in TestFlight.
3. As soon as a build finishes processing and clears export compliance, testers get a push notification from the TestFlight app — no review wait.

## 3. External testing (up to 10,000 testers, Beta App Review required)

External testing opens a build to people outside your team, via email invite or a public link:

```text
Upload build to TestFlight
        ↓
Add it to an external testing group, submit for Beta App Review
        ↓
Beta App Review (checks build stability and compliance — usually 24-48 hours)
        ↓ (approved)
Testers get access via email or the public link
```

> **Note:** Once an external group is approved for a marketing version (e.g., `1.2.0`), later builds for that same version (e.g., build `1.2.0.2`) usually don't need a full Beta App Review again.

## 4. Build expiration and feedback

- **Builds expire after 90 days.** After that, testers can't launch them anymore — you'll need to upload a fresh build.
- **TestFlight collects crash reports and screenshot feedback automatically.** You'll find them in App Store Connect under TestFlight → Crash Feedback.

## 5. Before you distribute

- [ ] Internal testers are assigned to a group for instant delivery.
- [ ] The first build for a new marketing version has been submitted for Beta App Review.
- [ ] The public link, if used, has a reasonable tester cap (e.g. 500) so it doesn't spread beyond your control.
- [ ] Someone's actually looking at TestFlight crash logs and feedback, not just collecting them.
- [ ] You're tracking the 90-day expiration window for builds still in active testing.

---

## Official sources

- TestFlight overview: https://developer.apple.com/testflight/

**Last verified:** August 14, 2026

---

## Related documentation

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
