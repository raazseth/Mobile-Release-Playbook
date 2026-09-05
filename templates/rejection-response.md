# Store Review Rejection Response Templates

Use these professional response templates to address rejection notices in **App Store Connect Resolution Center** and **Google Play Appeals**.

---

# 1. Guideline 2.1 (App Completeness / Demo Credentials Failure)

### App Store Connect Resolution Center Response

```text
Dear App Review Team,

Thank you for your feedback regarding Version [1.2.0] (Build [1.2.0.1]). We have addressed the authentication issue reported under Guideline 2.1.

Demo Account Credentials:
- Username: [demo_reviewer@example.com]
- Password: [DemoPassword2026!]
- 2FA Code / Bypass: Pre-configured demo account bypasses SMS 2FA. Enter '123456' for any OTP prompt.

Step-by-Step Verification Instructions:
1. Launch app version [1.2.0].
2. Enter the demo credentials listed above.
3. Tap 'Login' to enter the main app dashboard.
4. Tap 'Explore Features' on the bottom tab bar to test all primary functionality.

We have verified that these credentials are active on our production backend servers. We have attached a video recording demonstrating a successful login flow for your reference.

Thank you for your review.

Best regards,
[Your Name / Company Name]
```

---

# 2. Guideline 2.3.7 (Competitor Trademarks in Keywords)

```text
Dear App Review Team,

Thank you for bringing this to our attention. In accordance with Guideline 2.3.7, we have removed all trademarked terms from our App Store keywords field.

Applied Metadata Changes:
- Removed competitor brand keywords from the 100-character keyword field.
- Updated keywords field to: "fitness,tracker,workout,gym,log,exercise,calorie,weight"

We have saved the updated metadata in App Store Connect for Version [1.2.0] and resubmitted the app for review.

Thank you for your guidance.

Best regards,
[Your Name / Company Name]
```

---

# 3. Guideline 3.1.2 (Subscription Paywall Disclosure Missing)

```text
Dear App Review Team,

Thank you for your feedback regarding our subscription paywall presentation under Guideline 3.1.2.

We have submitted replacement build [1.2.0.2] which includes the following paywall updates:
1. Displayed subscription price ($9.99/month) and duration (1 Month Auto-Renewing) clearly above the purchase button.
2. Added functional links to our Privacy Policy ([https://example.com/privacy]) and Terms of Use (EULA) ([https://example.com/terms]) directly on the paywall screen.
3. Added explicit billing disclaimers explaining auto-renewal terms.

We have attached screenshots of the updated paywall screen for your review.

Thank you for your time.

Best regards,
[Your Name / Company Name]
```

---

# 4. Guideline 5.1.1 (Privacy Manifest / Data Collection Mismatch)

```text
Dear App Review Team,

Thank you for your review. We have addressed the data collection declaration mismatch under Guideline 5.1.1.

Applied Binary & Metadata Fixes:
1. Updated `PrivacyInfo.xcprivacy` in build [1.2.0.2] to explicitly declare crash reporting data collection (`NSPrivacyCollectedDataTypeCrashData`).
2. Aligned our App Privacy Labels in App Store Connect to reflect crash diagnostics data collection.
3. Added the required reason code (`NSPrivacyAccessedAPICategoryUserDefaults`) for User Defaults API access.

Build [1.2.0.2] has been attached to Version [1.2.0] and submitted for review.

Best regards,
[Your Name / Company Name]
```

---

# 5. Operational Verification Checklist

- [ ] **Polite & Technical Tone**: Written response polite, technical, and concise.
- [ ] **Exact Fix Stated**: Clear statement detailing exact code or metadata fix applied.
- [ ] **Video Proof Attached**: Screen recording attached if demonstrating complex setup or hardware feature.
- [ ] **Build Number Referenced**: New build number (`[1.2.0.2]`) referenced in response.

---

# 6. AI-Assisted Draft Generation

Don't draft this from scratch — [`ai/prompts/rejection-analysis.md`](../ai/prompts/rejection-analysis.md) already covers parsing a rejection notice and drafting the remediation response, in more depth than would fit here. Use it, then paste the result into whichever template above matches your guideline. The one rule that still applies regardless of which prompt you use: never let the draft claim a fix was made that wasn't actually verified.

---

# 7. Official Sources

- Apple App Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

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
- `templates/release-checklist.md`
- `templates/release-notes.md`
- `templates/release-plan.md`

### AI prompts

- `ai/prompts/rejection-analysis.md`

### Store operations

- `store-operations/README.md`

### Checklists

- `checklists/README.md`

### Post-release

- `post-release/incident-response.md`

### Release strategy

- `release-strategy/release-trains.md`
