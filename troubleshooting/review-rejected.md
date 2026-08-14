# App Review Rejection Triage & Resolution Protocols

This document details rejection triage procedures, Apple Guideline 2.1 (App Completeness), Guideline 3.1.1 (In-App Purchase), and Google Play policy strike triage for **App Review Rejections** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for triaging and resolving store review rejections.

This guide is **not**:

- an excuse to respond to store reviewers with emotional or hostile communication
- an authorization mechanism to immediately resubmit an identical binary without fixing reported bugs
- a substitute for verifying backend demo credentials

---

# 1. App Review Rejection Diagnostic Pipeline

```text
┌────────────────────────────────────────────────────────┐
│             APP REVIEW REJECTION DIAGNOSTIC            │
│                                                        │
│  - Extract exact Guideline violation (e.g., 2.1, 3.1.1)│
│  - Inspect attached reviewer screenshots or crash logs │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Guideline 2.1 Demo Login ]        [ Guideline 3.1.1 IAP ]
Verify demo credentials & 2FA       Replace external payment SDK
bypass on production API server     with StoreKit 2 / Play Billing 8
```

---

# 2. Key Rejection Scenarios & Resolution Protocols

### Scenario A: Guideline 2.1 (Demo Account Credentials Failed)
- **Root Cause**: Reviewer could not log in using provided demo credentials.
- **Resolution**:
  1. Test demo credentials on production server.
  2. Verify 2FA bypass is active for reviewer account.
  3. Provide step-by-step instructions in Resolution Center response.

### Scenario B: Guideline 3.1.1 (Digital Feature Sold via External Credit Card)
- **Root Cause**: App uses credit card SDK (Stripe/Braintree) to sell digital features.
- **Resolution**: Integrate StoreKit 2 / Play Billing 8.0+ for digital features; remove credit card checkout.

---

# 3. Operational Verification Checklist

- [ ] **Exact Guideline Diagnosed**: Rejection clause (e.g., 2.1, 3.1.1) identified before making edits.
- [ ] **Demo Credentials Active**: Demo account tested and functional on production server.
- [ ] **Response Professional**: Resolution Center response polite, technical, and concise.

---

# 4. Official Sources

- Apple App Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

---

**Last verified:** August 14, 2026
