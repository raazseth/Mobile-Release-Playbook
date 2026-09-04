# Google Play App Review

Every `.aab` you submit to Google Play goes through an automated pre-launch scan and a policy check. This covers what those checks look for and what to do if Google flags a violation.

This guide is **not**:

- an authorization mechanism to ignore a Google Play Developer Policy Center notice
- a substitute for actually reading your automated Firebase Test Lab crash reports
- a guide to working around a policy violation strike

---

## 1. What happens after you upload

```text
Upload .aab to Google Play Console
        ↓
Automated pre-launch report scan
  - runs the app on physical Android devices
  - scans for startup crashes, ANRs, rendering bugs
  - checks accessibility and security issues
        ↓
Google Play policy inspection
  - Data Safety form checked against requested permissions
  - target API level compliance checked
```

## 2. Where reviews most often get flagged

1. **Target API level.** Every submission needs to target the current required API level (Android 16 / API 36 as of this writing) — see [app-bundle.md](app-bundle.md) for the exact requirement and its yearly deadline.
2. **Data Safety form mismatches.** What you declare has to match what `AndroidManifest.xml` actually requests (location, contacts, `AD_ID`, etc.). This is one of the most common causes of a rejection that otherwise looks like a healthy app.
3. **Billing policy.** Digital goods need to go through Google Play Billing Library 8.0/9.0+, not an external payment flow.
4. **Account deletion.** If your app lets users create an account, you need both an in-app and a web account-deletion path.

## 3. Handling a rejection, warning, or removal notice

1. Read the Play Console inbox notification carefully — it names the specific policy clause (Impersonation, Deceptive Behavior, Unapproved Permissions, etc.), and that wording is what you're actually responding to.
2. Fix the underlying cause: update `AndroidManifest.xml`, drop permissions you don't need, or fix the store listing text.
3. If you think the violation was issued in error, file a formal appeal through the **Google Play Policy Appeal Form** with concrete technical evidence — a vague appeal rarely goes anywhere.

For the broader rejection-handling workflow across both stores, see [store-operations/rejection-handling.md](../../store-operations/rejection-handling.md).

## 4. Before you submit

- [ ] The pre-launch report is clean — no startup crashes or ANRs.
- [ ] Data Safety declarations match `AndroidManifest.xml` permissions exactly.
- [ ] The web-based account deletion URL actually works.
- [ ] `targetSdkVersion` matches the current requirement.
- [ ] High-risk permissions (`SMS`, `CALL_LOG`, `QUERY_ALL_PACKAGES`) are either justified in your submission notes or removed.

---

## Official sources

- Google Play Policy Center: https://play.google.com/about/developer-content-policy/
- Google Play pre-launch report guide: https://support.google.com/googleplay/android-developer/answer/9842757

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/closed-testing.md`
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

### Troubleshooting

- `troubleshooting/review-rejected.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
