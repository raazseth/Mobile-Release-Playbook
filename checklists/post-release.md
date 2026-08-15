# Post-Release Checklist

Use this checklist after a mobile app release is live on the App Store or Google Play.

The goal is to confirm that the released build works in production, detect problems early, understand user impact, and respond safely when something goes wrong.

This checklist is framework-independent. Expo and React Native are first-class, but the same release principles apply to other mobile frameworks.

> **Important:** Store dashboards, rollout controls, analytics, and platform behavior can change. Use the official Apple and Google documentation linked at the end when a step depends on current platform behavior.

---

## 1. Confirm the release is live

Before monitoring the release, confirm that the intended version is actually available.

### iOS

- [ ] The intended version is available on the App Store.
- [ ] The intended build is attached to the released version.
- [ ] The store listing shows the expected version and metadata.
- [ ] The app can be installed from the App Store.
- [ ] The production app launches successfully.

### Android

- [ ] The intended version is available on Google Play.
- [ ] The intended release/build is the one being distributed.
- [ ] The store listing shows the expected version and metadata.
- [ ] The app can be installed from Google Play.
- [ ] The production app launches successfully.

Do not assume that an approved or published release is immediately available to every user. Distribution and update timing can vary by platform and user.

---

## 2. Install the public release

Test the exact version users receive from the store.

- [ ] Fresh install works.
- [ ] App launches.
- [ ] App does not crash on first launch.
- [ ] Production environment is active.
- [ ] Production API is reachable.
- [ ] Primary user journey works.
- [ ] Login/signup works where applicable.
- [ ] Logout works.
- [ ] Account recovery works where applicable.
- [ ] Core data loads correctly.
- [ ] Important actions complete successfully.

Do not use a development build for this verification.

The store-installed build is the source of truth for post-release verification.

---

## 3. Verify the most important user journey

Choose the one flow that represents the core value of the app.

For example:

```text
Install
  ↓
Open
  ↓
Sign in
  ↓
Perform primary action
  ↓
Receive expected result
```

Verify it on:

- [ ] iOS
- [ ] Android

If this flow fails in production, treat it as a release incident rather than a normal bug.

---

## 4. Check production health

Review the systems the app depends on.

- [ ] API health is normal.
- [ ] Authentication service is healthy.
- [ ] Database health is normal.
- [ ] Important background jobs are healthy.
- [ ] Third-party services are responding.
- [ ] Error rates are within the expected range.
- [ ] Latency is within the expected range.
- [ ] No unusual traffic or resource usage is visible.
- [ ] No production configuration changed unexpectedly.

If the mobile release depends on a backend, monitor the backend as part of the release.

A mobile release can be healthy while a backend change breaks the same user journey.

---

## 5. Check crash and error reporting

Review the crash-reporting system used by the project.

Examples include:

- Apple crash information
- Google Play quality information
- Firebase Crashlytics
- Sentry
- another approved monitoring system

Check:

- [ ] Crash reporting is receiving data.
- [ ] The new release is identified correctly.
- [ ] New crash types are reviewed.
- [ ] Crash volume is compared with the previous release.
- [ ] Crash-free users/sessions are checked where the tool provides them.
- [ ] Crashes affecting the primary flow are prioritized.
- [ ] Native crashes are reviewed.
- [ ] JavaScript/runtime crashes are reviewed where applicable.
- [ ] Error spikes are investigated.
- [ ] Known crashes are not incorrectly treated as new issues.

Do not rely on a single metric.

A low crash count does not prove the release is healthy if a critical user flow is failing without crashing.

---

## 6. Check application performance

Review production performance.

- [ ] App launch time is acceptable.
- [ ] Important screens load within expected limits.
- [ ] API requests are completing normally.
- [ ] Slow endpoints are identified.
- [ ] Excessive network requests are not occurring.
- [ ] Memory problems are not visible.
- [ ] Battery-heavy behavior is not visible.
- [ ] Large downloads/uploads behave normally.
- [ ] Background processing behaves normally where applicable.

Compare the new release with the previous release when the monitoring system supports version-level comparison.

Focus on user impact rather than collecting metrics that no one will act on.

---

## 7. Verify analytics

Confirm that important production events are being recorded.

- [ ] App launch event works.
- [ ] Signup event works where applicable.
- [ ] Login event works where applicable.
- [ ] Primary action event works.
- [ ] Purchase/subscription events work where applicable.
- [ ] Important conversion events work.
- [ ] Critical error events work where applicable.
- [ ] Events are associated with the correct release/version.
- [ ] No sensitive data is being sent accidentally.
- [ ] No unexpected event volume spike exists.

Analytics should be checked for correctness, not just availability.

---

## 8. Check push notifications

If the app uses push notifications:

- [ ] Notifications are being delivered.
- [ ] Notification permission behavior is correct.
- [ ] Foreground behavior works.
- [ ] Background behavior works.
- [ ] Terminated-app behavior works.
- [ ] Notification taps open the correct destination.
- [ ] Notification tokens are being registered correctly.
- [ ] Logout/account switching does not leak notifications.
- [ ] Notification delivery failures are monitored where possible.

Test with a real production account/device.

---

## 9. Check deep links

If the app uses deep links:

- [ ] Production links open the app.
- [ ] Links open the correct destination.
- [ ] Links work from a cold start.
- [ ] Links work when the app is already running.
- [ ] Authentication-required links behave correctly.
- [ ] Invalid links fail safely.
- [ ] Universal Links work on iOS where used.
- [ ] Android App Links work on Android where used.

Use production domains for this check.

---

## 10. Check payments and subscriptions

If the app uses in-app purchases or subscriptions:

- [ ] Purchase flow is working.
- [ ] Successful transactions grant the correct entitlement.
- [ ] Failed transactions do not grant access incorrectly.
- [ ] Subscription state is correct.
- [ ] Cancellations are handled.
- [ ] Expiration is handled.
- [ ] Restore/recovery works where applicable.
- [ ] Backend entitlement state is correct where applicable.
- [ ] Transaction validation is working where required.
- [ ] No unusual payment error rate is visible.

Do not use the mobile client as the sole authority for valuable entitlements.

---

## 11. Review store ratings and feedback

User feedback is an important production signal.

### iOS

- [ ] Review recent ratings.
- [ ] Review recent written reviews.
- [ ] Look for repeated complaints.
- [ ] Identify release-specific complaints.
- [ ] Respond where appropriate.
- [ ] Escalate reports that indicate a production issue.

App Store Connect provides ratings, reviews, and review filtering by version, country/region, and rating.

### Android

- [ ] Review recent ratings.
- [ ] Review recent written reviews.
- [ ] Look for repeated complaints.
- [ ] Identify release-specific complaints.
- [ ] Respond where appropriate.
- [ ] Escalate reports that indicate a production issue.

Google Play Console provides ratings and review analysis, including current and historical rating information.

Do not manipulate ratings or reviews.

Do not pay for, fake, or artificially incentivize reviews.

Google's policy explicitly prohibits manipulation of ratings, reviews, and install counts.

---

## 12. Monitor rollout health

If the release is being rolled out progressively, monitor it before increasing exposure.

Track:

```text
Users exposed
    ↓
Crash/error rate
    ↓
Core-flow success
    ↓
Performance
    ↓
Support/reviews
    ↓
Business-critical events
```

Before increasing the rollout:

- [ ] No critical crash increase.
- [ ] No critical error increase.
- [ ] Core user journey is healthy.
- [ ] Backend is healthy.
- [ ] No serious user reports are emerging.
- [ ] Performance remains acceptable.
- [ ] Payment flows are healthy where applicable.
- [ ] No security issue is known.
- [ ] The release owner has reviewed the evidence.

### Android staged rollouts

Google Play supports staged rollouts for app updates. The rollout percentage does not increase automatically. Google recommends closely monitoring crash reports and user feedback during a staged rollout.

If a serious issue is found:

- [ ] Halt the staged rollout.
- [ ] Confirm no additional users are receiving the affected version.
- [ ] Identify affected users and systems.
- [ ] Prepare a fix.

Halting a staged rollout does not remove the version from users who already received it.

Google also provides a mechanism to halt a fully rolled-out release in eligible cases. This can prevent new or existing users from installing or updating to the affected version, but it is not a universal rollback mechanism and has important limitations.

---

## 13. Monitor store visibility

If users report that the app or update is missing:

- [ ] Confirm the release is published.
- [ ] Confirm the correct countries/regions are selected.
- [ ] Confirm the user's device is supported.
- [ ] Confirm the user is not receiving a testing version.
- [ ] Confirm the store listing is active.
- [ ] Confirm the update has finished processing/distribution where applicable.

Google notes that app updates can take time to reach existing users and that device compatibility can affect app visibility.

Do not immediately assume the release is broken.

First determine whether the issue is:

```text
Store availability
vs
Distribution delay
vs
Device compatibility
vs
Release configuration
vs
Actual application failure
```

---

## 14. Check support and incident reports

Review the first production reports from:

- [ ] Support tickets.
- [ ] User emails.
- [ ] In-app feedback.
- [ ] Store reviews.
- [ ] Internal reports.
- [ ] Monitoring alerts.
- [ ] Crash reports.

Group reports by:

```text
Same symptom
    ↓
Same version
    ↓
Same platform
    ↓
Same device/OS
    ↓
Same feature
```

This helps distinguish a single user issue from a release-wide problem.

---

# 15. Release incident triage

When something breaks, do not immediately start changing code.

First determine:

### Impact

- How many users are affected?
- Is the primary user journey affected?
- Is data at risk?
- Is money involved?
- Is authentication affected?
- Is the issue platform-specific?
- Is the issue release-specific?

### Severity

Use a simple classification:

```text
P0
Critical production failure or security issue

P1
Major feature or large user impact

P2
Important but limited impact

P3
Minor issue or improvement
```

Prioritize using:

```text
Likelihood × Impact × Exposure
```

Do not prioritize only because an issue looks technically interesting.

---

## 16. Incident response

For a serious issue:

```text
Detect
  ↓
Triage
  ↓
Contain
  ↓
Diagnose
  ↓
Fix
  ↓
Test
  ↓
Release
  ↓
Verify
  ↓
Document
```

### Containment comes first

Possible containment actions include:

- [ ] Halt a rollout where the platform supports it.
- [ ] Disable a risky feature using an existing feature flag.
- [ ] Disable a broken backend capability.
- [ ] Restrict a failing workflow.
- [ ] Revert a compatible backend change.
- [ ] Prevent additional users from entering a broken flow.

Do not introduce a new emergency infrastructure system during an incident unless there is a clear need.

---

## 17. Security incident

If the release exposes:

- API keys
- private credentials
- authentication tokens
- personal data
- financial information
- signing credentials
- production secrets

treat it as a security incident.

Use:

```text
Contain
  ↓
Revoke
  ↓
Rotate
  ↓
Investigate
  ↓
Recover
  ↓
Communicate
  ↓
Document
```

Do not wait for a complete root-cause analysis before revoking an exposed secret.

Never put exposed credentials into an issue, chat, log, or AI prompt.

---

## 18. Hotfix readiness

If a code fix is required:

- [ ] Root cause is understood enough to make a safe fix.
- [ ] Fix is small and focused where possible.
- [ ] Regression test is added where practical.
- [ ] Fix is tested on the affected platform.
- [ ] Production configuration is unchanged unless required.
- [ ] Version/build number is updated correctly.
- [ ] Release notes are prepared.
- [ ] Store submission requirements are checked.
- [ ] Human approval is obtained before production submission.

A hotfix should solve the production problem without introducing unrelated changes.

---

## 19. Store rollback limitations

Do not assume mobile rollback works like a server deployment rollback.

A previously released mobile version may already be installed on users' devices.

Therefore:

- [ ] Know whether the affected version can be halted.
- [ ] Know whether the platform can serve an earlier version to new/eligible users.
- [ ] Know whether existing users remain on the affected version.
- [ ] Prepare a fixed release when rollback cannot fully remove the problem.
- [ ] Coordinate backend compatibility with older and newer app versions.

For Android, Google documents specific halt behavior for staged and fully rolled-out releases, including limitations.

For iOS, do not describe a generic "rollback" as if an older App Store binary can simply be restored to all users. Plan for mitigation and a new release when necessary.

---

## 20. Monitor the hotfix

After a hotfix:

- [ ] Confirm the fixed version is live.
- [ ] Install it from the store.
- [ ] Verify the original failure is resolved.
- [ ] Verify the primary user journey.
- [ ] Check crash/error data.
- [ ] Check backend health.
- [ ] Monitor user reports.
- [ ] Monitor store reviews.
- [ ] Confirm no new major regression appeared.

Do not close the incident immediately after the build is published.

Confirm the production behavior.

---

## 21. Version compatibility

After release, verify that backend changes remain compatible with supported mobile versions.

Check:

- [ ] Current production app works with the backend.
- [ ] Older supported app versions still work where required.
- [ ] API changes are backward compatible where necessary.
- [ ] Database migrations do not break older clients.
- [ ] Feature flags do not expose unsupported behavior.
- [ ] Forced-update logic works if the product uses it.
- [ ] Deprecated API versions are not removed prematurely.

Mobile users update at different times.

Design backend changes with the supported client-version window in mind.

---

## 22. Review release metrics

After the initial release window:

- [ ] Compare crash/error rates with the previous release.
- [ ] Compare important performance metrics.
- [ ] Compare core-flow success.
- [ ] Compare conversion metrics where applicable.
- [ ] Compare support volume.
- [ ] Review store ratings and reviews.
- [ ] Review rollout behavior.
- [ ] Identify regressions.
- [ ] Identify improvements.

For Apple, App Store Connect Analytics provides data across discovery, downloads, engagement, purchases, and subscriptions.

For Google Play, release details include release-level information such as installs/updates, performance issues, and ratings compared with previous releases.

Only track metrics that can lead to an action.

---

## 23. Update documentation

If the release exposed a new problem:

- [ ] Add a troubleshooting entry if the problem is reusable.
- [ ] Update the relevant framework guide if the issue is framework-specific.
- [ ] Update the checklist if a release step was missing.
- [ ] Update source references if platform behavior changed.
- [ ] Add a note to the changelog for meaningful documentation changes.
- [ ] Record the root cause where it will help future releases.

Do not document a one-off incident as a permanent platform requirement.

---

## 24. Release retrospective

After the release has stabilized, record:

### What worked

- [ ] Successful checks.
- [ ] Useful automation.
- [ ] Effective testing.
- [ ] Good monitoring signals.

### What failed

- [ ] Missed release checks.
- [ ] Production-only failures.
- [ ] Store problems.
- [ ] Monitoring gaps.
- [ ] Process problems.

### What should change

- [ ] Add a test.
- [ ] Improve a checklist.
- [ ] Improve monitoring.
- [ ] Improve release automation.
- [ ] Improve documentation.
- [ ] Remove unnecessary steps.

Keep the retrospective focused on improving the release process.

---

# AI-assisted post-release analysis

AI can help reduce the time required to understand production signals.

Useful tasks include:

- grouping crash reports
- summarizing error trends
- comparing release versions
- finding likely regressions
- analyzing support reports
- clustering store reviews
- drafting incident summaries
- preparing release retrospectives
- identifying repeated troubleshooting patterns
- generating a first-pass hotfix investigation plan

A safe workflow is:

```text
Production signals
      ↓
AI analysis
      ↓
Candidate findings
      ↓
Developer verification
      ↓
Root cause
      ↓
Fix
      ↓
Test
      ↓
Human approval
```

AI must not be treated as the source of truth for production incidents.

Do not give an AI agent unrestricted access to:

- production credentials
- signing keys
- store accounts
- private customer data
- production databases
- destructive production tools

If production data is sent to an AI system, first verify that the data handling is appropriate for the system, account, and organization's privacy requirements.

---

# 25. Post-release security review

After a significant release, check:

- [ ] No new secrets were accidentally committed.
- [ ] Production logs do not expose sensitive information.
- [ ] New permissions are expected.
- [ ] New third-party SDKs are expected.
- [ ] New network destinations are expected.
- [ ] New authentication behavior is correct.
- [ ] New deep links do not create unintended access.
- [ ] New exported/native capabilities are expected.
- [ ] Build and CI permissions remain least-privilege.
- [ ] No unexpected production access was introduced.

For a high-risk release, perform a deeper security review before increasing rollout.

---

# 26. Final post-release gate

The release is considered stable when:

```text
Release live
    ↓
Store installation verified
    ↓
Core flow verified
    ↓
Crash/error data reviewed
    ↓
Performance reviewed
    ↓
Backend health reviewed
    ↓
Store feedback reviewed
    ↓
Rollout reviewed
    ↓
No unresolved critical issue
    ↓
Documentation updated
    ↓
Release closed
```

Before closing the release:

- [ ] No critical production issue remains.
- [ ] No unresolved security incident remains.
- [ ] Monitoring is working.
- [ ] Important user flows are healthy.
- [ ] Store distribution is behaving as expected.
- [ ] Any hotfix has been verified.
- [ ] Relevant documentation has been updated.
- [ ] The release owner has closed the release.

---

# Official sources

Use the official sources below for current platform behavior.

### Apple

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- App Store Connect Analytics: https://developer.apple.com/help/app-store-connect-analytics/
- Ratings and Reviews: https://developer.apple.com/help/app-store-connect/monitor-ratings-and-reviews/
- App Store Connect API: https://developer.apple.com/help/app-store-connect/manage-app-information/view-app-analytics/

### Google

- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Prepare and roll out a release: https://support.google.com/googleplay/android-developer/answer/9859348
- Staged rollouts: https://support.google.com/googleplay/android-developer/answer/6346149
- Halt a fully rolled-out release: https://support.google.com/googleplay/android-developer/answer/16285429
- Ratings and reviews: https://support.google.com/googleplay/android-developer/answer/138230
- App visibility and discovery: https://support.google.com/googleplay/android-developer/answer/9042516

### Expo

- Distribution overview: https://docs.expo.dev/distribution/introduction/
- EAS Update: https://docs.expo.dev/eas-update/introduction/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Submit: https://docs.expo.dev/submit/introduction/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

Platform behavior and release tooling can change. Re-check the official sources when a post-release workflow depends on current store capabilities.
