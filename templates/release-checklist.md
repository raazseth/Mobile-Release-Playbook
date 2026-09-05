# Master Pre-Release & Execution Checklist

Use this operational checklist to execute pre-release audits, binary compilation verifications, store review submissions, and production staged rollouts.

---

# Release Meta Information

- **App Name**: `[App Name]`
- **Target Release Version**: `v[1.2.0]`
- **iOS Build Number**: `[1.2.0.1]` | **Android versionCode**: `[143]`
- **Date**: `[YYYY-MM-DD]`

---

# 1. Pre-Build Audit & Code Freeze Phase

- [ ] **Branch Merged**: All release features merged into `main` / `release/v1.2.0` branch.
- [ ] **Dependencies Audited**: Zero vulnerable or un-vetted npm/cocoapods dependencies.
- [ ] **Target API 36 Set**: `targetSdkVersion 36` verified in `android/app/build.gradle`.
- [ ] **Privacy Manifests Included**: `PrivacyInfo.xcprivacy` verified for all compiled iOS SDKs.
- [ ] **Export Compliance Set**: `ITSAppUsesNonExemptEncryption: false` configured in `Info.plist`.
- [ ] **Version Strings Monotonic**: `versionCode` and `buildNumber` incremented strictly.

---

# 2. Build Compilation & Internal Testing Phase

- [ ] **iOS `.ipa` Compiled**: Production `.ipa` generated cleanly without Xcode warnings.
- [ ] **Android `.aab` Compiled**: Production `.aab` compiled with R8 minification (`minifyEnabled true`).
- [ ] **R8 Mapping File Uploaded**: ProGuard `mapping.txt` uploaded to Sentry / Crashlytics.
- [ ] **dSYM Files Uploaded**: iOS `.dSYM` debug symbols uploaded to Sentry / Crashlytics.
- [ ] **Internal QA Pass**: 100% core user flows (Login, Search, Checkout) verified on TestFlight / Internal track.

---

# 3. Store Metadata & Media Asset Verification Phase

- [ ] **App Title Length**: Title ≤ 30 characters on both App Store Connect and Google Play Console.
- [ ] **Subtitle / Short Description**: Subtitle ≤ 30c (iOS); Short Description ≤ 80c (Android).
- [ ] **Keywords Formatted**: iOS keywords ≤ 100c, comma-separated without spaces or trademarks.
- [ ] **Alpha Transparency Stripped**: All PNG screenshots verified free of alpha transparency (`hasAlpha: NO`).
- [ ] **Canonical 6.9" Screenshots Included**: 1320x2868 px screenshots uploaded for iOS 6.9" display class.
- [ ] **Feature Graphic Uploaded**: 1024x500 px Feature Graphic uploaded for Google Play.
- [ ] **Privacy Policy & Support URLs Live**: Web URLs tested and returning HTTP 200 OK.

---

# 4. Store Submission & Review Phase

- [ ] **Demo Credentials Active**: Demo account credentials (`[username] / [password]`) verified working on production backend.
- [ ] **2FA Bypass Verified**: Reviewer demo account bypasses SMS/email 2FA prompts.
- [ ] **Reviewer Notes Detailed**: Reviewer notes provide step-by-step navigation instructions.
- [ ] **Build Submitted for Review**: Build submitted to App Store Review and Google Play Review.

---

# 5. Production Release Execution Phase

- [ ] **App Review Approved**: Review approval notifications received from Apple and Google Play.
- [ ] **Phased Release Enabled (iOS)**: 7-Day Phased Release initiated (Day 1: 1%).
- [ ] **Staged Rollout Enabled (Android)**: Staged Rollout initiated at 1% or 5%.
- [ ] **Crashlytics Monitored**: Crash-free user metric monitored daily (Target: > 99.0%).
- [ ] **Full Release Completed**: Release expanded to 100% of users on Day 7.

---

# Sign-Off Verification

- **Release Manager Approval**: `[ Signature / Date ]`
- **Lead QA Engineer Approval**: `[ Signature / Date ]`

---

# 6. AI-Assisted Draft Generation

Use this prompt to generate a checklist scoped to what this specific release actually touches — a release with no native dependency changes doesn't need the same items as one that upgrades the Android target API. Have QA confirm the scoped list still covers everything before treating it as final.

```markdown
<system_instructions>
You are a release engineering assistant. Given the release scope below, produce a scoped
version of the Master Pre-Release & Execution Checklist: keep every item from the template
that applies, drop items that clearly don't apply given the stated scope, and add any item
the scope implies that the template doesn't already cover (e.g. a new push notification
provider implies checking APNs/FCM credentials). Explain briefly why anything was dropped.
</system_instructions>

<release_scope>
- App name / version / build numbers: <APP_NAME_VERSION_BUILDS>
- What changed in this release: <LIST — e.g. "new payment SDK, no native dependency changes, no target API bump">
- Platforms affected: <iOS | Android | Both>
</release_scope>
```

- [ ] Anything the AI dropped from the checklist was actually verified as not applicable, not just trusted.
- [ ] QA Lead reviewed the scoped checklist before it replaces the full one for this release.

---

**Last verified:** September 5, 2026

---

# Related documentation

### Templates

- `templates/README.md`
- `templates/app-store-description.md`
- `templates/changelog.md`
- `templates/incident-report.md`
- `templates/play-store-description.md`
- `templates/privacy-questionnaire.md`
- `templates/rejection-response.md`
- `templates/release-notes.md`
- `templates/release-plan.md`

### AI prompts

- `ai/prompts/release-audit.md`
- `ai/prompts/qa.md`

### Store operations

- `store-operations/README.md`

### Checklists

- `checklists/README.md`

### Post-release

- `post-release/incident-response.md`

### Release strategy

- `release-strategy/release-trains.md`
