# Resolution Center Triage & Rejection Handling Handbook

This handbook details the triage procedures, rejection code taxonomy, Resolution Center communication protocols, and formal appeal workflows for **App Store & Google Play Rejections** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive technical and operational guidance for systematically handling rejections, addressing store review feedback, and submitting formal appeals without triggering escalations.

This guide is **not**:

- an excuse to respond to store reviewers with emotional or hostile communication
- an authorization mechanism to immediately re-submit an identical binary without fixing the reported bug
- a substitute for reading full, un-truncated store rejection logs

---

# 1. Rejection Triage Architecture

When a build is rejected by Apple App Review or Google Play Review, the status changes to **Rejected** or **Action Required**, and a detailed message is posted in App Store Connect Resolution Center or Google Play Console Inbox.

```text
[ App Submission Rejected by Store Review ]
                     │
                     ▼
┌────────────────────────────────────────────────────────┐
│             RESOLUTION CENTER DIAGNOSIS                │
│  - Extract exact Guideline violation (e.g., 2.1, 5.1.1)│
│  - Inspect attached reviewer screenshots or crash logs │
└──────────────────────────┬─────────────────────────────┘
                     │
         ┌───────────┴───────────┐
         ▼                       ▼
 [ Binary Defect ]       [ Clarification Needed ]
 Fix code defect in JS   Respond in Resolution Center
 or native code & rebuild with video/docs explanation
```

---

# 2. Rejection Code Taxonomy & Triage Protocols

| Rejection Code / Guideline | Violation Root Cause | Immediate Action Required |
|---|---|---|
| **Guideline 2.1 (App Completeness)** | App crashed on launch, demo account failed, or feature incomplete | Test demo account; inspect Sentry/Crashlytics logs; fix crash & rebuild. |
| **Guideline 2.3 (Accurate Metadata)** | Competitor name in keywords, misleading screenshot, or price in title | Remove trademarked keywords; update screenshots; adjust title. |
| **Guideline 3.1.1 (In-App Purchase)** | Digital feature sold via credit card or missing StoreKit 2 integration | Replace credit card SDK with StoreKit 2 / Play Billing 8.0+. |
| **Guideline 3.1.2 (Subscriptions)** | Paywall missing auto-renewal disclosure, price, duration, or Privacy/EULA links | Add mandatory paywall disclosures and Privacy Policy & Terms links. |
| **Guideline 5.1.1 (Data Collection)** | Missing usage description string in Plist or un-declared `PrivacyInfo.xcprivacy` API | Add usage string to `Info.plist`; update `PrivacyInfo.xcprivacy` manifest. |

---

# 3. Resolution Center Communication Standard

When responding to store reviewers in App Store Connect Resolution Center or Google Play Appeals:

```text
┌────────────────────────────────────────────────────────┐
│             PROFESSIONAL RESPONSE FORMAT               │
│                                                        │
│  1. Acknowledge Feedback: Thank the review team.       │
│  2. State Exact Fix: Clearly state what code or        │
│     metadata change was applied in the new build.      │
│  3. Provide Proof: Attach video link or screenshot     │
│     demonstrating the resolution.                      │
│  4. Provide Build Info: Reference the new build        │
│     version/build number submitted for review.         │
└──────────────────────────┬─────────────────────────────┘
```

---

# 4. Formal Board of Appeals Protocol

If you believe your app was rejected due to a reviewer misunderstanding or incorrect policy interpretation:

1. **Submit Reviewer Clarification First**: Reply in Resolution Center asking for specific clarification.
2. **Submit Formal Appeal**: If clarification fails, submit a formal appeal via the **App Review Board** (`developer.apple.com/contact/app-store/appeal/`) or Google Play Appeal form.
3. **Provide Legal / Functional Context**: Reference specific guideline text, industry precedent, and technical video demonstrations.

---

# 5. Operational Verification Checklist

- [ ] **Exact Guideline Identified**: Specific guideline code (e.g., Guideline 5.1.1) diagnosed before making code edits.
- [ ] **Root Cause Fixed**: Binary code defect or metadata inaccuracy fixed before resubmitting.
- [ ] **Resolution Center Response Professional**: Written response concise, polite, and technical.
- [ ] **Video Proof Attached**: Screen recording attached if demonstrating complex setup or hardware feature.
- [ ] **Build Number Bumped**: Build number incremented for replacement binary upload.

---

# 6. Related Documentation

- [Resubmission Handbook](resubmission.md) - Resubmitting builds.
- [App Review Handbook](app-review.md) - Review submission.
- [Rejection Analysis Prompt](../ai/prompts/rejection-analysis.md) - AI rejection triage.

---

# 7. Official Sources

- Apple App Review Board Appeals: https://developer.apple.com/contact/app-store/appeal/
- Google Play Policy Appeals: https://support.google.com/googleplay/android-developer/troubleshooter/2993242

---

**Last verified:** August 14, 2026
