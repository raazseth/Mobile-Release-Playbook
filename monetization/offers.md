# Subscription Offers & Promotional Codes

This document details the architectural design, offer types (Introductory Offers, Promotional Offers, Win-Back Offers, Offer Codes), cryptographic signature generation, and compliance rules for **Subscription Offers & Discounts** in Expo and React Native applications.

It specifies how to configure promotional pricing to drive user acquisition and win-back retention while ensuring compliance with Apple App Store Review Guidelines (Guideline 3.1.2) and Google Play Offer policies.

This guide is **not**:

- an authorization mechanism to generate promo codes without store setup
- a client-side signature generator (iOS promotional offer signatures MUST be generated on a secure backend server)
- a substitute for verifying offer eligibility

---

# 1. Offer Taxonomy & Classification

Mobile app stores support four distinct types of promotional offers:

```text
                               SUBSCRIPTION OFFER TYPES
                                          │
       ┌──────────────────────┬───────────┴───────────┬──────────────────────┐
       ▼                      ▼                       ▼                      ▼
[ Introductory Offer ]   [ Promotional Offer ]   [ Win-Back Offer ]     [ Offer / Promo Code ]
New subscribers only.    Existing / past subs.   Lapsed subscribers.    Custom alphanumeric
Free trial / Pay-as-you-go. StoreKit 2 signature. Target churned users. code (Redeem in store).
```

| Offer Type | Target Audience | iOS Verification Mechanism | Android Verification |
|---|---|---|---|
| **Introductory Offer** | New users (never subscribed) | Automatic StoreKit check (`isEligibleForIntroOffer`) | Automatic Play Billing check |
| **Promotional Offer** | Existing or former subscribers | Server-signed cryptographic JWS signature | Play Billing Offer ID |
| **Win-Back Offer** | Lapsed subscribers (churned) | StoreKit 2 automatic eligibility check | Play Billing Win-Back Offer |
| **Offer / Promo Code** | Any targeted marketing segment | One-time custom alphanumeric code | Play Console Promo Code |

---

# 2. Cryptographic Signature Generation for iOS Promotional Offers

Unlike introductory offers, iOS Promotional Offers require a **cryptographic signature** generated on your backend server using an App Store Connect Subscription Private Key (`.p8`).

```text
[ App Requests Promotional Offer ]
                │
                ▼
[ Backend API Generates Signature Payload ]
(AppID + ProductID + OfferID + Nonce + Timestamp + KeyID)
                │
                ▼
[ Sign Payload using Private Key (.p8) ]
                │
                ▼
[ Return ECDSA Signature to Mobile Client ]
                │
                ▼
[ Client Submits Signature to StoreKit 2 Purchase API ]
```

### Required Signature Parameters

```javascript
// Backend Node.js Signature Generation Example
const crypto = require('crypto');

function generatePromotionalOfferSignature({
  appBundleId,
  keyId,
  productIdentifier,
  offerIdentifier,
  applicationUsername,
  nonce,
  timestamp,
  privateKeyPem,
}) {
  // Combine parameters in exact Apple specification order
  const payload = [
    appBundleId,
    keyId,
    productIdentifier,
    offerIdentifier,
    applicationUsername,
    nonce,
    timestamp,
  ].join('\u2060'); // Invisible separator character

  const sign = crypto.createSign('SHA256');
  sign.update(payload);
  return sign.sign(privateKeyPem, 'base64');
}
```

> **SECURITY MANDATE**: Never store the Subscription Private Key (`.p8`) inside the mobile app binary. Always generate promotional offer signatures on a secure backend server.

---

# 3. Code Implementation Example (Offer Code Redemption)

Both iOS and Android support in-app presentment of store offer code redemption sheets:

```typescript
import Purchases from 'react-native-purchases';
import { Platform } from 'react-native';

export async function presentStorefrontOfferCodeRedemption() {
  if (Platform.OS === 'ios') {
    // Displays official Apple App Store Offer Code redemption sheet
    await Purchases.presentCodeRedemptionSheet();
  }
}
```

---

# 4. Operational Verification Checklist

- [ ] **Private Key Secure**: `.p8` subscription key stored exclusively in backend secret vault; never embedded in app code.
- [ ] **Eligibility Verified**: Client checks user eligibility before displaying introductory or win-back offer badges.
- [ ] **Offer Signature Validated**: Promotional offer ECDSA signatures pass StoreKit validation without `StoreKitError`.
- [ ] **Clear Offer Terms**: Paywall clearly discloses regular price after trial or discount period ends.
- [ ] **Store Console Product Synced**: Offer IDs in app code match configured offer IDs in App Store Connect and Google Play Console.

---

# 5. Related Documentation

- [Subscriptions Handbook](subscriptions.md) - Subscriptions setup.
- [Subscription Lifecycle Handbook](subscription-lifecycle.md) - Lifecycle states.
- [Receipt Validation Handbook](receipt-validation.md) - Server verification.

---

# 6. Official Sources

- Apple Set Up Subscription Offers: https://developer.apple.com/documentation/storekit/in-app_purchase/original_api_for_in-app_purchase/subscriptions_and_offers/setting_up_promotional_offers
- Google Play Billing Offers: https://developer.android.com/google/play/billing/subscriptions#offers

---

**Last verified:** August 14, 2026

