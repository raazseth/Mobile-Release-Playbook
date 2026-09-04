# Android Closed Testing

Closed Testing lets you distribute a pre-release build to a defined list of external testers before it reaches open testing or production. For Personal developer accounts, it's also a mandatory gate — you can't get to production without clearing it first.

This guide is **not**:

- an authorization mechanism to fake tester opt-ins
- a substitute for actually watching what real testers report
- an optional step for Personal developer accounts

---

## 1. What closed testing is for

```text
Closed testing track (Alpha/Beta): access restricted to designated Google Groups
or lists, subject to automated Google Play policy checks, testers submit feedback
directly via Play Store
        │
        ↓ (Personal developer accounts only)
Mandatory testing gate: 12 opted-in testers, 14 continuous days opted-in,
required before production track access is granted
```

## 2. The Personal-account gate: 12 testers, 14 days

Google Play Console requires Personal developer accounts created after November 2023 to clear this before they can publish to production:

1. Recruit 12 testers who accept the closed testing invitation link.
2. Keep them opted in for 14 continuous days — if someone opts out, they don't count toward the total for that stretch.
3. Testers should actually download and use the app during that window, not just accept the invite and ignore it.

Once that's done, apply for production track access, which triggers a manual review from Google.

> **Note:** Organization developer accounts (D-U-N-S verified) are exempt from this 14-day gate and get immediate access to production tracks.

## 3. Managing the group and its feedback

- Link a Google Group (e.g., `beta-testers@googlegroups.com`) to automatically grant testing access to everyone in it, instead of adding testers one by one.
- Play Store gives closed testers a private feedback channel, so bugs and crash reports come to you directly instead of landing as public reviews.

## 4. Before you apply for production access

- [ ] A closed (alpha or beta) track exists in Play Console.
- [ ] At least 12 testers are opted in.
- [ ] The 14-continuous-day clock has actually run its course for Personal accounts — don't cut it short.
- [ ] The pre-launch report is clean of crash spikes during the closed testing window.
- [ ] Tester feedback has been reviewed and, where it matters, addressed before you move on.

---

## Official sources

- Google Play closed testing requirements: https://support.google.com/googleplay/android-developer/answer/9845334

**Last verified:** August 14, 2026

---

## Related documentation

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
