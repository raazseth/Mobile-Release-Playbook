# App Review Submission Preparation & Reviewer Notes Handbook

This handbook details the review submission workflows, demo account credential setup, Reviewer Notes specifications, TestFlight export compliance declarations, and submission checks for **App Store & Google Play Review Submissions** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive technical and operational guidance for preparing flawless review submissions to pass store inspection without avoidable delays.

This guide is **not**:

- an authorization mechanism to submit builds without pre-submission QA verification
- a guide to obfuscating features from reviewers (Apple Guideline 2.3.1 strictly forbids hidden features)
- a substitute for providing active demo login credentials

---

# 1. Review Submission Pipeline & Verification Gates

App Store and Google Play review teams inspect build binaries, UI navigation, in-app purchases, and backend network endpoints during the evaluation window.

```text
[ Build Uploaded to Store Console (App Store Connect / Play Console) ]
                                   │
                                   ▼
┌────────────────────────────────────────────────────────┐
│            PRE-SUBMISSION CHECKLIST VERIFICATION       │
│  - Active Demo Credentials (Username + Password)       │
│  - Reviewer Notes detailing test steps & 2FA bypass    │
│  - Export Compliance & Encryption Declarations         │
└──────────────────────────┬─────────────────────────────┘
                                   │
                                   ▼
[ Submit for Review → Review In Progress → Approved / Rejected ]
```

---

# 2. Reviewer Notes & Mandatory Demo Credentials (Guideline 2.1)

Submitting an app requiring user login without functional demo credentials will trigger an immediate rejection under **Apple Guideline 2.1 (App Completeness)**:

```text
┌────────────────────────────────────────────────────────┐
│             REVIEWER NOTES STANDARD TEMPLATE           │
│                               ━━━━━━━━━━━━━━━━        │
│  Demo Account Credentials:                             │
│  - Username: review_demo@example.com                   │
│  - Password: DemoPassword2026!                         │
│  - 2FA Code / Bypass: Pre-configured demo account      │
│    bypasses SMS 2FA. Enter '123456' for any OTP prompt.│
│                                                        │
│  Step-by-Step Testing Flow:                            │
│  1. Log in with demo account.                          │
│  2. Tap 'Explore Features' on main tab bar.            │
│  3. Tap 'Try Premium' to test StoreKit 2 Sandbox IAP. │
└────────────────────────────────────────────────────────┘
```

> **CRITICAL MANDATE**: Demo accounts MUST remain active throughout the entire submission review lifecycle. Deleting or blocking demo accounts on backend servers during review causes instant rejection.

---

# 3. Export Compliance & Encryption Declarations (`ITSAppUsesNonExemptEncryption`)

To prevent automated export compliance prompts when submitting iOS builds:

Include `ITSAppUsesNonExemptEncryption: false` in `Info.plist` (via Expo `app.json` config):

```json
{
  "expo": {
    "ios": {
      "infoPlist": {
        "ITSAppUsesNonExemptEncryption": false
      }
    }
  }
}
```

---

# 4. App Store Review Information Configuration

In App Store Connect under **App Review Information**:

1. **Sign-In Required**: Check box if app requires authentication; enter demo credentials.
2. **Contact Information**: Enter first/last name, corporate email, and phone number of a release engineer authorized to speak with Apple App Review.
3. **Notes**: Provide step-by-step navigation instructions, hardware requirements, or links to video demonstrations for complex setups.
4. **Attachment**: Upload hardware architecture diagrams or PDF guides if app requires physical IoT hardware.

---

# 5. Operational Verification Checklist

- [ ] **Demo Credentials Active**: Demo username/password tested and working on production API servers.
- [ ] **2FA Bypass Configured**: Reviewer demo account bypasses SMS/email 2FA prompts.
- [ ] **Reviewer Notes Detailed**: Reviewer notes contain step-by-step navigation instructions.
- [ ] **Export Compliance Declared**: `ITSAppUsesNonExemptEncryption` configured in `Info.plist`.
- [ ] **Backend Endpoints Live**: Production/staging backend APIs remain 100% operational throughout review.
- [ ] **Contact Info Updated**: Authorized engineer phone number and email current in App Review Info.

---

# 6. Related Documentation

- [Rejection Handling Handbook](rejection-handling.md) - Rejection triage.
- [Resubmission Handbook](resubmission.md) - Resubmitting builds.
- [Release Preparation Workflow](../../ai/workflows/release-preparation.md) - Pre-release checks.

---

# 7. Official Sources

- Apple App Review Information Help: https://developer.apple.com/help/app-store-connect/#/dev02c0507a2
- Google Play App Review Submission: https://support.google.com/googleplay/android-developer/answer/9859751

---

**Last verified:** August 14, 2026
