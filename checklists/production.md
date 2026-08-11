# Production Checklist

Use this checklist when moving a mobile release into production and during the first production rollout.

This checklist is the **production gate**. It focuses on the final decision to release, production rollout, monitoring, and response readiness.

It does not replace:

- `checklists/pre-release.md` for release readiness
- `checklists/ios.md` for iOS-specific requirements
- `checklists/android.md` for Android-specific requirements
- `checklists/post-release.md` for detailed post-release monitoring

The checklist is framework-independent, with first-class support for Expo and React Native.

> **Important:** Store rules, rollout controls, SDK requirements, and tooling behavior can change. Verify current platform-specific requirements against the official sources before release.

---

## 1. Production release candidate

- [ ] The exact release commit is known.
- [ ] The exact iOS build is known.
- [ ] The exact Android build is known.
- [ ] Production configuration is confirmed.
- [ ] Version and build numbers are recorded.
- [ ] Release notes match the actual release.
- [ ] No unrelated changes are included.
- [ ] Required backend changes are deployed or ready.
- [ ] The release owner is identified.

Record:

```text
Version:
iOS build:
Android version code:
Commit:
Build profile:
Release date:
Release owner:
```

The production release must be traceable to a specific source version and build artifact.

---

## 2. Final release gate

Before releasing, confirm the pre-release checks have passed.

- [ ] `checklists/pre-release.md` is complete.
- [ ] iOS checklist is complete if shipping iOS.
- [ ] Android checklist is complete if shipping Android.
- [ ] Critical release blockers are resolved.
- [ ] Known exceptions are explicitly accepted by the appropriate owner.
- [ ] Security review is complete.
- [ ] Privacy review is complete.
- [ ] Store metadata is final.
- [ ] Store testing is complete.
- [ ] Human approval has been given.

If a critical requirement cannot be verified:

> **Not enough evidence yet.**

Stop the release and investigate.

---

## 3. Production backend readiness

A mobile release often depends on backend services.

Before release:

- [ ] Production API is healthy.
- [ ] Required endpoints are deployed.
- [ ] Database migrations are complete.
- [ ] Database migrations are backward compatible where required.
- [ ] Required third-party services are available.
- [ ] Authentication services are healthy.
- [ ] Required background jobs are healthy.
- [ ] Feature flags are configured.
- [ ] Rate limits are appropriate.
- [ ] No planned maintenance conflicts with the release.
- [ ] Backend supports the new mobile version.
- [ ] Older supported mobile versions remain compatible where required.

Do not assume that a successful mobile build means the complete product is ready for production.

---

## 4. Configuration and environment

- [ ] Production environment is selected.
- [ ] Production API URLs are correct.
- [ ] Development URLs are absent.
- [ ] Staging credentials are absent.
- [ ] Production feature flags are correct.
- [ ] Required third-party production configuration is present.
- [ ] Analytics configuration is correct.
- [ ] Crash reporting configuration is correct.
- [ ] Push notification configuration is correct where applicable.
- [ ] Deep-link configuration is correct where applicable.
- [ ] Payment configuration is correct where applicable.

### Expo

For Expo:

- [ ] Correct EAS project is selected.
- [ ] Correct production build profile is selected.
- [ ] Resolved app configuration is correct.
- [ ] iOS Bundle ID is correct.
- [ ] Android application ID is correct.
- [ ] Production credentials are configured securely.

Do not rely only on the source configuration file. Verify the configuration used by the actual production build.

---

## 5. Signing and secrets

### iOS

- [ ] Production signing is valid.
- [ ] Correct Apple Developer team is used.
- [ ] Required capabilities are configured.
- [ ] App Store distribution signing is correct.

### Android

- [ ] Production signing is valid.
- [ ] Upload key is available securely.
- [ ] Play App Signing configuration is correct where applicable.

### Secrets

- [ ] No production secrets are committed to Git.
- [ ] No private signing keys are committed.
- [ ] CI/CD secrets are protected.
- [ ] Store credentials are protected.
- [ ] Access follows least privilege.
- [ ] Credential recovery and rotation are understood.

Never put secrets into:

- source code
- public configuration
- logs
- issue reports
- screenshots
- AI prompts

If a production secret is exposed:

```text
Revoke
  ↓
Rotate
  ↓
Investigate
  ↓
Verify
```

---

## 6. Production artifact verification

Verify the exact artifacts being released.

### iOS

- [ ] Correct Bundle ID.
- [ ] Correct version.
- [ ] Correct build number.
- [ ] Correct signing.
- [ ] Correct entitlements.
- [ ] Production configuration.
- [ ] No development endpoints.
- [ ] No debug-only functionality.

### Android

- [ ] Correct application ID.
- [ ] Correct version.
- [ ] Correct version code.
- [ ] Correct target API level.
- [ ] Correct signing.
- [ ] Production configuration.
- [ ] No development endpoints.
- [ ] No debug-only functionality.

Do not approve a release based only on the fact that the build command succeeded.

---

## 7. Production smoke test

Test the actual release candidate or store-installed version.

- [ ] App installs.
- [ ] App launches.
- [ ] No first-launch crash.
- [ ] Authentication works.
- [ ] Primary user journey works.
- [ ] Important error states work.
- [ ] API requests work.
- [ ] App restart works.
- [ ] Background/foreground behavior works.
- [ ] Logout works.
- [ ] Account switching works where applicable.

### Platform coverage

- [ ] iOS tested on a real device if shipping iOS.
- [ ] Android tested on a real device if shipping Android.

A simulator or emulator is useful, but it is not enough for final production approval.

---

## 8. Production observability

Before release, confirm that serious failures can be detected.

- [ ] Crash reporting is active.
- [ ] Error monitoring is active.
- [ ] Production API errors are visible.
- [ ] Important backend health metrics are available.
- [ ] Release/version can be identified in monitoring.
- [ ] Important analytics events are working.
- [ ] Critical alerts are configured.
- [ ] Logs are accessible to the people responsible for the release.
- [ ] Sensitive data is not exposed in logs.

Monitor user impact, not only infrastructure metrics.

The key question is:

> **Can we detect a serious release problem quickly enough to act?**

---

## 9. Release rollout

Choose the appropriate production rollout strategy.

Possible approaches:

```text
Full release
    or
Staged / phased rollout
```

Before increasing exposure:

- [ ] Initial rollout size is understood.
- [ ] Rollout controls are understood.
- [ ] Monitoring is active.
- [ ] Support is ready.
- [ ] Incident response is ready.
- [ ] The release owner is available.

For a high-risk release, prefer a controlled rollout when the platform and product allow it.

Do not use a staged rollout simply because it sounds safer. Use it when the team can actually monitor the rollout and act on the results.

---

## 10. Human production approval

The final production action must be deliberate.

Before submitting or releasing:

- [ ] Exact build has been reviewed.
- [ ] Store metadata has been reviewed.
- [ ] Rollout settings have been reviewed.
- [ ] Production backend is healthy.
- [ ] Monitoring is active.
- [ ] No critical release blocker exists.
- [ ] Human release owner approves production release.

Use:

```text
AI / automation assists
        ↓
Human verifies
        ↓
Human approves
        ↓
Production release
```

Do not recommend unrestricted AI access to production release controls.

---

## 11. AI-assisted production audit

AI can help perform the final audit before release.

Useful tasks:

- compare release configuration
- inspect Expo/EAS configuration
- find development endpoints
- check version configuration
- review dependency changes
- inspect permissions
- review deep links
- review notification configuration
- draft release notes
- summarize known release risks
- prepare a release-readiness report

A safe workflow is:

```text
Repository
   ↓
AI audit
   ↓
Candidate findings
   ↓
Developer verifies
   ↓
Fix
   ↓
Build
   ↓
Test
   ↓
Human approval
   ↓
Release
```

AI-generated commands, policy interpretations, and release recommendations are untrusted until verified.

Never give an AI agent:

- signing private keys
- store passwords
- production tokens
- production database access
- unrestricted deployment permissions
- destructive production tools

---

## 12. Production release

When the release is approved:

```text
Final verification
      ↓
Submit / publish
      ↓
Confirm release state
      ↓
Install public version
      ↓
Smoke test
      ↓
Monitor
```

After publishing:

- [ ] Release state is confirmed.
- [ ] Public version is visible as expected.
- [ ] Store installation works.
- [ ] Production app launches.
- [ ] Primary user journey works.
- [ ] Monitoring is receiving data.
- [ ] No immediate critical error appears.

Do not assume that a successful upload means the release is live.

---

## 13. Initial monitoring window

Monitor the release closely after launch.

Check:

- [ ] Crash rate.
- [ ] Error rate.
- [ ] Core user-flow success.
- [ ] API health.
- [ ] Latency.
- [ ] Authentication failures.
- [ ] Payment failures where applicable.
- [ ] Push notification behavior where applicable.
- [ ] Deep-link behavior where applicable.
- [ ] Store reviews.
- [ ] Support reports.
- [ ] Important analytics events.

Compare the new release with the previous production version where meaningful comparison is available.

Look for changes, not just absolute numbers.

---

## 14. Rollout decision

If the release is staged or phased, decide whether to continue.

### Continue when

- [ ] No critical crash increase.
- [ ] No critical error increase.
- [ ] Primary user journey is healthy.
- [ ] Backend is healthy.
- [ ] Performance is acceptable.
- [ ] No serious security issue is known.
- [ ] No repeated release-specific user complaint is emerging.

### Stop or limit when

- [ ] Critical user journey is broken.
- [ ] Significant crash increase appears.
- [ ] Authentication is broken.
- [ ] Payments are broken.
- [ ] Sensitive data is exposed.
- [ ] Security vulnerability is discovered.
- [ ] Backend incompatibility affects users.
- [ ] A large number of users are affected.

Do not increase rollout based only on the absence of obvious errors.

Use actual production evidence.

---

## 15. Incident response

If the release causes a serious problem:

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
Release hotfix
  ↓
Verify
```

### First priority: contain the impact

Possible actions:

- [ ] Halt rollout where supported.
- [ ] Disable a risky feature using an existing feature flag.
- [ ] Restrict the affected backend flow.
- [ ] Revert a compatible backend change.
- [ ] Prepare a hotfix.

Do not wait for a perfect root-cause analysis before taking a safe containment action.

---

## 16. Security incident

If the release exposes a:

- secret
- signing credential
- authentication token
- personal data
- financial data
- private key
- production credential

treat it as a security incident.

- [ ] Contain exposure.
- [ ] Revoke affected credentials.
- [ ] Rotate credentials.
- [ ] Investigate impact.
- [ ] Check logs and access records.
- [ ] Fix the release.
- [ ] Verify recovery.
- [ ] Document the incident.

Never publish exposed credentials in an issue or troubleshooting document.

---

## 17. Hotfix readiness

If a hotfix is needed:

- [ ] Root cause is understood enough to make a safe change.
- [ ] Fix is focused.
- [ ] Regression test is added where practical.
- [ ] Fix is tested on the affected platform.
- [ ] New build/version identifiers are correct.
- [ ] Production configuration is reviewed.
- [ ] Store submission requirements are checked.
- [ ] Human approval is obtained.

Avoid unrelated changes in an emergency release.

---

## 18. Backend compatibility after release

Continue supporting the mobile versions that users still have installed.

- [ ] Current release works with production APIs.
- [ ] Older supported releases remain compatible where required.
- [ ] API changes are backward compatible where necessary.
- [ ] Database migrations do not break older clients.
- [ ] Feature flags are compatible with supported versions.
- [ ] Deprecated API versions are not removed prematurely.
- [ ] Forced-update behavior works if the product uses it.

Mobile users do not update at the same time.

Treat client-version compatibility as a production concern.

---

## 19. Production data and privacy

After release:

- [ ] No unexpected sensitive data is being logged.
- [ ] Analytics does not contain unintended personal data.
- [ ] Crash reports do not expose secrets.
- [ ] Production telemetry is access-controlled.
- [ ] Data retention follows the product's requirements.
- [ ] New third-party services behave as expected.
- [ ] Privacy declarations still match actual behavior.

If the release changes data collection, review the relevant store privacy information before or alongside the release.

---

## 20. Release closure

Once the release is stable:

- [ ] Critical issues are resolved or explicitly accepted.
- [ ] Monitoring has returned to normal operation.
- [ ] Hotfixes are documented.
- [ ] Important troubleshooting knowledge is documented.
- [ ] Relevant checklist changes are made.
- [ ] Meaningful documentation changes are added to `CHANGELOG.md`.
- [ ] Release metrics have been reviewed.
- [ ] Release owner closes the release.

Useful retrospective questions:

```text
What failed?
What caught it?
What did not catch it?
What should become a permanent check?
What should be removed or simplified?
```

Do not turn every incident into a permanent process step. Add a new check only when it prevents a realistic recurring failure.

---

# Final production gate

The release is ready to go live when:

```text
Exact build known
      ↓
Production configuration verified
      ↓
Signing verified
      ↓
Smoke test passed
      ↓
Backend healthy
      ↓
Monitoring active
      ↓
Recovery path understood
      ↓
Store state verified
      ↓
Human approval
      ↓
PRODUCTION RELEASE
```

After release:

```text
Release
  ↓
Verify
  ↓
Monitor
  ↓
Assess
  ↓
Continue / Stop / Fix
```

If a critical item is unknown, stop.

> **Not enough evidence yet.**

---

# Related checklists

Use these together:

- `checklists/first-release.md` — first production launch
- `checklists/pre-release.md` — final release readiness
- `checklists/ios.md` — iOS-specific release checks
- `checklists/android.md` — Android-specific release checks
- `checklists/cross-platform.md` — shared iOS + Android checks
- `checklists/post-release.md` — detailed post-release monitoring

---

# Official sources

Use official documentation for current platform behavior.

### Apple

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- App Review: https://developer.apple.com/app-store/review/
- Submit an app: https://developer.apple.com/help/app-store-connect/manage-submissions-to-app-review/submit-an-app
- App Store Connect workflow: https://developer.apple.com/help/app-store-connect/get-started/app-store-connect-workflow

### Google

- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Prepare and roll out a release: https://support.google.com/googleplay/android-developer/answer/9859348
- Staged rollouts: https://support.google.com/googleplay/android-developer/answer/6346149
- Target API requirements: https://developer.android.com/google/play/requirements/target-sdk

### Expo

- Distribution overview: https://docs.expo.dev/distribution/introduction/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Submit: https://docs.expo.dev/submit/introduction/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

Store requirements, rollout behavior, SDK requirements, and release tooling can change. Re-check the relevant official source before every production release.
