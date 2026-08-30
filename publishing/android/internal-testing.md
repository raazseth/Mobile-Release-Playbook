# Android Internal Testing Track Administration

This document covers setup, tester lists, and automated CI/CD uploading for the **Google Play Internal Testing Track** — how to distribute test builds to internal QA teams and developers without a store review delay.

This guide is **not**:

- an authorization mechanism to bypass production QA validation
- a substitute for closed or open beta testing tracks
- a public release channel (Internal Testing is restricted to designated internal email lists)

---

# 1. Internal Testing Architecture & Delivery Speed

The Internal Testing Track is Google Play's fastest distribution channel. Builds uploaded to the internal track bypass standard store review and become available to internal testers within minutes.

```text
Developer / CI tool uploads .aab to the internal track
        ↓
Instant distribution: no review delay, up to 100 testers per list,
testers access the build via a Play Store opt-in link
        ↓
Internal QA and developers receive the update immediately
```

---

# 2. Tester List Management & Email Opt-In

To grant testers access to the Internal Testing track:

1. **Create Tester List**: In Google Play Console -> Testing -> Internal testing -> Testers tab, create an email list (e.g., `qa-team@company.com`).
2. **Add Tester Emails**: Add up to 100 Google account email addresses per list.
3. **Copy Join Link**: Share the official **Opt-in URL** (`https://play.google.com/apps/internaltest/...`) with testers. Testers MUST open this link once on their Android device to accept the internal testing invitation.

---

# 3. Automated Continuous Integration Deployment

Configure CI/CD toolchains to deploy every successful main-branch build to the Internal Testing track automatically:

```bash
# Automated deployment to Internal Track via Fastlane Supply
bundle exec fastlane supply --track internal --aab android/app/build/outputs/bundle/release/app-release.aab
```

---

# 4. Operational Verification Checklist

- [ ] **Opt-In Link Shared**: Internal testers clicked the opt-in URL on their Android devices.
- [ ] **Tester Emails Added**: Internal tester list configured in Play Console.
- [ ] **Instant Delivery Verified**: Updated builds appear in Play Store for internal testers within 15 minutes.
- [ ] **In-App Feedback Enabled**: Internal testers can submit feedback directly through Play Store.
- [ ] **CI Pipeline Automated**: Merge to `main` automatically uploads `.aab` to internal testing.

---

# Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/app-review.md`
- `publishing/android/closed-testing.md`
- `publishing/android/metadata.md`
- `publishing/android/play-console.md`
- `publishing/android/production-release.md`
- `publishing/android/screenshots.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Android signing

- `signing/android/README.md`
- `signing/android/play-app-signing.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Google Play Internal Testing Guide: https://support.google.com/googleplay/android-developer/answer/9845334

---

**Last verified:** August 14, 2026

