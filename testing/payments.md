# In-App Purchase & Subscription Testing in Sandbox Environments

This document details sandbox testing procedures, StoreKit 2 local configuration files (`.storekit`), Google Play Billing 8.0/9.0+ License Testing, and subscription lifecycle validation for **Payments & Monetization Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test digital purchases, paywalls, and receipt validations safely before submitting to store review.

This guide is **not**:

- an authorization mechanism to test real payment credit cards on production storefronts
- a substitute for testing StoreKit 2 Signed JWS transaction verification
- a guide to bypassing Guideline 3.1.1 or Play Billing policy rules

---

# 1. StoreKit 2 Local Sandbox Testing (`.storekit`)

Xcode allows testing StoreKit 2 transactions locally without connecting to App Store Connect servers by creating a **StoreKit Configuration File** (`Configuration.storekit`).

```text
┌────────────────────────────────────────────────────────┐
│             STOREKIT 2 LOCAL SANDBOX TESTING           │
│                                                        │
│  - Define Products: Consumables, Non-Consumables, Subs │
│  - Test Auto-Renewal: Accelerated subscription time   │
│    (1 Month Subscription = 5 Minutes in Sandbox)       │
│  - Test Interrupted Purchases & Refunds                │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Simulated StoreKit 2 Purchase Window Renders in Xcode ]
```

---

# 2. Google Play License Testing Setup

To test Google Play Billing 8.0/9.0+ transactions without real credit card charges:

1. **Add Test Accounts**: Add tester Gmail addresses to Google Play Console -> Setup -> License Testing.
2. **License Response**: Set License Response to `RESPOND_NORMALLY`.
3. **Test Track Execution**: Install `.aab` compiled build on a test device logged into a License Tester Google account. Test purchases will process via Google Play Sandbox ("Test Card, Always Approves").

---

# 3. Operational Verification Checklist

- [ ] **StoreKit 2 Sandbox Verified**: Purchases verified using local `.storekit` config and TestFlight sandbox.
- [ ] **Play License Accounts Added**: Tester emails configured under Play Console License Testing.
- [ ] **Restore Purchases Tested**: Non-consumable and subscription restore purchases verified working.
- [ ] **Accelerated Renewals Handled**: Subscription renewal and expiration states tested.
- [ ] **Paywall Disclosures Present**: Paywall displays price, duration, Privacy Policy, and EULA links.

---

# 4. Official Sources

- Apple StoreKit 2 Testing Guide: https://developer.apple.com/documentation/storekit/in-app_purchase/testing_in-app_purchases_with_storekit_in_xcode
- Google Play Billing Testing Guide: https://developer.android.com/google/play/billing/testing

---

**Last verified:** August 14, 2026
