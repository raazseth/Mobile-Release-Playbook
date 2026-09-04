# Android Production Release and Staged Rollout

A staged rollout releases a production update to a small slice of users first, so you can catch a bad build before it reaches everyone. This covers the rollout schedule, what to watch during it, and how to halt one if something goes wrong.

This guide is **not**:

- an authorization mechanism to release an un-tested build straight to 100% of users
- a substitute for watching crash rates in Sentry or Firebase Crashlytics during a rollout
- a guide to bypassing review requirements

---

## 1. How a staged rollout progresses

```text
Staged rollout lifecycle
  Stage 1:  1% or 5%  → initial health check
  Stage 2: 10% or 20% → scale monitoring
  Stage 3: 50%        → final stability check
  Stage 4: 100%       → full release
        │
        ├─→ crash rate stable    → increase the percentage
        └─→ crash spike detected → halt the rollout immediately
```

## 2. A rollout schedule to work from

| Stage | Target percentage | Minimum time before expanding | What to check |
|---|---|---|---|
| Stage 1 | 1–5% | 24 hours | Below Google Play's published Android Vitals bad-behavior thresholds (ANR rate 0.47%, crash rate 1.09% — see Official Sources), no critical payment crashes |
| Stage 2 | 10–20% | 24 hours | Crash-free session rate stable in your crash reporter; no new error spikes |
| Stage 3 | 50% | 24 hours | Android Vitals still within acceptable thresholds |
| Stage 4 | 100% | — | Rollout complete; close the release ticket |

Android Vitals' bad-behavior thresholds are account-wide quality bars, not thresholds Google publishes per rollout stage — using them as a stage-1 checkpoint is this playbook's own heuristic, not an official Google requirement.

## 3. Halting a rollout

If a crash spike, memory leak, or backend failure shows up during a rollout:

```text
Play Console ──→ [ Production Track → Edit Release ] ──→ [ Click "Halt Rollout" ]
```

Halting stops **new** users from getting the update — anyone who already updated stays on it until you publish a replacement build with a higher build number. Once halted, fix the bug, bump `versionCode`, upload a new `.aab`, and start a fresh rollout at 1% or 5%.

## 4. Before you expand a rollout

- [ ] The rollout starts at 1% or 5%, not higher.
- [ ] Android Vitals is checked in Play Console — user-perceived ANR rate under 0.47%, crash rate under 1.09%.
- [ ] Your crash reporter shows a crash-free session rate above 99.2%.
- [ ] Someone on the team actually knows how to halt a rollout, not just that it's possible.
- [ ] The final push to 100% has been reviewed and approved before you flip it.

---

## Official sources

- Google Play staged rollout guide: https://support.google.com/googleplay/android-developer/answer/6346149
- Android Vitals: https://developer.android.com/topic/performance/vitals
- Android Vitals bad-behavior thresholds (crash/ANR rate): https://support.google.com/googleplay/android-developer/answer/9844486

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/app-review.md`
- `publishing/android/closed-testing.md`
- `publishing/android/internal-testing.md`
- `publishing/android/metadata.md`
- `publishing/android/play-console.md`
- `publishing/android/screenshots.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Android signing

- `signing/android/README.md`
- `signing/android/play-app-signing.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Post-release

- `post-release/README.md`
- `troubleshooting/crash-after-release.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
