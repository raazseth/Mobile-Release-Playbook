# Android Closed Testing Tracks & Personal Account Testing Gates

This document covers closed beta track administration, Alpha/Beta testing channels, and the mandatory Personal-account closed testing gate (12 opted-in testers for 14 continuous days) for **Google Play Closed Testing Tracks** — how to satisfy closed testing requirements before requesting production access.

This guide is **not**:

- an authorization mechanism to fake tester opt-ins
- a substitute for conducting real user testing
- an optional requirement for Personal developer accounts

---

# 1. Closed Testing Track Architecture

Closed Testing Tracks allow developers to distribute pre-release App Bundles to designated lists of external beta testers or organization groups before deploying to open testing or production.

```text
Closed testing track (Alpha/Beta): access restricted to designated Google Groups
or lists, subject to automated Google Play policy checks, testers submit feedback
directly via Play Store
        │
        ↓ (Personal developer accounts only)
Mandatory testing gate: 12 opted-in testers, 14 continuous days opted-in,
required before production track access is granted
```

---

# 2. Personal Developer Account Closed Testing Gate (12 Testers / 14 Days)

Google Play Console enforces mandatory testing requirements for Personal developer accounts created after November 2023:

```text
1. Recruit 12 opted-in testers: they must accept the closed testing invitation link.
2. 14 continuous days: testers stay opted-in without opting out for 14 consecutive days.
3. Active engagement: testers should download and use the app during that window.
        │
        ↓
Apply for production track access → manual Google review evaluation
```

> **COMMERCIAL EXEMPTION**: Organization Developer Accounts (D-U-N-S verified) are **exempt** from this 14-day personal testing gate and have immediate access to production release tracks.

---

# 3. Managing Closed Testing Groups & Feedback

1. **Google Groups Integration**: Link a Google Group (e.g., `beta-testers@googlegroups.com`) to automatically grant testing access to all group members.
2. **Private Feedback Channel**: Play Store provides a private feedback mechanism allowing closed testers to report crashes and UI bugs directly to the developer without impacting public store ratings.

---

# 4. Operational Verification Checklist

- [ ] **Closed Track Created**: Alpha or Beta closed testing track created in Play Console.
- [ ] **12+ Testers Opted-In**: Minimum 12 testers opted-in via closed testing link.
- [ ] **14-Day Clock Tracked**: 14 continuous days monitored for Personal account requirements.
- [ ] **Pre-Launch Report Clean**: Pre-launch report reviewed for crash spikes during closed testing.
- [ ] **Feedback Monitored**: Private tester feedback reviewed and addressed before production release.

---

# Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/app-review.md`
- `publishing/android/internal-testing.md`
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

- Google Play Closed Testing Requirements: https://support.google.com/googleplay/android-developer/answer/9845334

---

**Last verified:** August 14, 2026

