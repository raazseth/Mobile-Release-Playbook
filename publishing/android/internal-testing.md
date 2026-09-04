# Android Internal Testing Track

Internal Testing is Google Play's fastest distribution channel — a build uploaded here skips store review entirely and reaches testers within minutes. This covers setting it up and wiring it into CI.

This guide is **not**:

- an authorization mechanism to skip production QA
- a substitute for closed or open beta testing
- a public release channel — Internal Testing is restricted to the email list you define

---

## 1. Why it's fast

Builds uploaded to the internal track skip standard store review and become available to internal testers within minutes.

```text
Developer / CI tool uploads .aab to the internal track
        ↓
Instant distribution: no review delay, up to 100 testers per list,
testers access the build via a Play Store opt-in link
        ↓
Internal QA and developers receive the update immediately
```

## 2. Setting up testers

1. In Google Play Console → Testing → Internal testing → Testers tab, create an email list (e.g., `qa-team@company.com`).
2. Add up to 100 Google account email addresses to that list.
3. Share the **Opt-in URL** (`https://play.google.com/apps/internaltest/...`) with testers — each one needs to open it once on their Android device to accept the invitation. Without that step, the build just won't show up for them.

## 3. Automating it in CI

Wire every successful main-branch build to deploy to Internal Testing automatically:

```bash
# Deploy to the internal track via Fastlane Supply
bundle exec fastlane supply --track internal --aab android/app/build/outputs/bundle/release/app-release.aab
```

Or with `eas submit --platform android --profile internal` if you're on EAS — see [release-engineering/eas/eas-submit.md](../../release-engineering/eas/eas-submit.md) for the full submit-profile setup.

## 4. Before you rely on it

- [ ] Testers have actually clicked the opt-in URL — invites don't take effect until they do.
- [ ] The internal tester list is current in Play Console.
- [ ] A newly uploaded build shows up in the Play Store app for testers within 15 minutes.
- [ ] Testers know they can submit feedback directly through the Play Store app.
- [ ] Merging to `main` (or your release branch) triggers an automatic `.aab` upload to internal testing.

---

## Official sources

- Google Play internal testing guide: https://support.google.com/googleplay/android-developer/answer/9845334

**Last verified:** August 14, 2026

---

## Related documentation

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
