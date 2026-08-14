# Android Internal Testing Track Administration

This document details the setup, tester distribution lists, instant deployment mechanics, automated CI/CD uploading, and QA feedback collection for the **Google Play Internal Testing Track**.

Engineered in alignment with **2026 platform specifications**, it specifies how to rapidly distribute test builds to internal QA teams and developers without undergoing store review delays.

This guide is **not**:

- an authorization mechanism to bypass production QA validation
- a substitute for closed or open beta testing tracks
- a public release channel (Internal Testing is restricted to designated internal email lists)

---

# 1. Internal Testing Architecture & Delivery Speed

The Internal Testing Track is Google Play's fastest distribution channel. Builds uploaded to the internal track bypass standard store review and become available to internal testers within minutes.

```text
[ Developer / CI Tool Uploads `.aab` to Internal Track ]
                          │
                          ▼
┌────────────────────────────────────────────────────────┐
│             INSTANT DISTRIBUTION ENGINE                │
│  - Zero App Review Delay (Instant availability)        │
│  - Supports up to 100 Internal Testers per list        │
│  - Testers access builds via Play Store opt-in link    │
└──────────────────────────┬─────────────────────────────┘
                          │
                          ▼
[ Internal QA & Developers Receive Immediate App Update ]
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

# 5. Related Documentation

- [Closed Testing Handbook](closed-testing.md) - Closed testing gates.
- [Play Console Handbook](play-console.md) - Service accounts.
- [Production Release Handbook](production-release.md) - Staged rollouts.

---

# 6. Official Sources

- Google Play Internal Testing Guide: https://support.google.com/googleplay/android-developer/answer/9845334

---

**Last verified:** August 14, 2026
