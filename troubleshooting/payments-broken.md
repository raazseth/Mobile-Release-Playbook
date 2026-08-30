# In-App Purchase & Subscription Failure Triage

This document details diagnostic workflows, StoreKit 2 JWS signature verification errors, Google Play Billing 8.0/9.0+ response code 7 (`ITEM_ALREADY_OWNED`), and paywall submission rejections for **Payments Troubleshooting** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for resolving mobile app commerce failures.

This guide is **not**:

- an authorization mechanism to bypass StoreKit 2 Signed JWS transaction verification
- a substitute for testing sandbox purchases
- a guide to misrepresenting paywall subscription terms

---

# 1. Payment & Monetization Failure Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             PAYMENT FAILURE DIAGNOSTIC FLOW            │
│                                                        │
│  - Inspect StoreKit 2 transaction logs or Play Billing │
│  - Extract exact store response error code             │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ StoreKit 2 JWS Verification Error ] [ Play Billing `ITEM_ALREADY_OWNED` ]
Server root certificate missing;    Consume consumable purchase via
verify App Store Server API key     `consumeAsync` or acknowledge sub
```

---

# 2. Key Payment Errors & Resolutions

### Scenario A: StoreKit 2 `VerificationResult.unverified`
- **Root Cause**: Transaction JWS payload failed cryptographic signature verification against Apple's Root CA.
- **Resolution**: Fetch Apple Root Certificates; verify JWS signature header using StoreKit 2 native verification APIs before granting entitlements.

### Scenario B: Google Play Billing `Response Code 7: ITEM_ALREADY_OWNED`
- **Root Cause**: Consumable purchase was not consumed on backend server after purchase completion.
- **Resolution**: Call `BillingClient.consumeAsync()` for consumables or `acknowledgePurchase()` for non-consumables/subscriptions within 3 days to prevent automatic refund by Google Play.

---

# 3. Operational Verification Checklist

- [ ] **JWS Verification Active**: StoreKit 2 transactions verified using Apple JWS signature verification.
- [ ] **Play Purchases Acknowledged**: Purchases consumed or acknowledged to prevent auto-refunds.
- [ ] **Sandbox Purchases Verified**: Purchases tested on StoreKit Sandbox and Play License Testing.

---

# 4. Official Sources

- Apple StoreKit 2 Verification: https://developer.apple.com/documentation/storekit/verificationresult
- Google Play Billing Response Codes: https://developer.android.com/google/play/billing/billing_reference

---

**Last verified:** August 14, 2026

---

# Related documentation

### Troubleshooting

- `troubleshooting/README.md`
- `troubleshooting/app-not-visible.md`
- `troubleshooting/archive-fails.md`
- `troubleshooting/build-fails.md`
- `troubleshooting/ci-fails.md`
- `troubleshooting/crash-after-release.md`
- `troubleshooting/deep-links-broken.md`
- `troubleshooting/metadata-rejected.md`
- `troubleshooting/privacy-rejected.md`
- `troubleshooting/push-notifications-broken.md`
- `troubleshooting/review-rejected.md`
- `troubleshooting/signing-fails.md`
- `troubleshooting/upload-fails.md`

### Signing

- `signing/README.md`

### Publishing

- `publishing/README.md`

### Store operations

- `store-operations/rejection-handling.md`

### Post-release

- `post-release/crash-analysis.md`
- `post-release/monitoring.md`

### Testing

- `testing/README.md`
