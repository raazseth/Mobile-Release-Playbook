# In-App Purchases Architecture & Compliance

This document details the architectural foundation, native API integrations (StoreKit 2 for iOS, Google Play Billing Library 8.0/9.0+ for Android), product types, and store review compliance rules for **In-App Purchases (IAP)** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to structure digital commerce, manage product catalogs, and comply with Apple App Store Review Guidelines (Guideline 3.1.1) and Google Play Payments policies.

This guide is **not**:

- an authorization mechanism to bypass store commission fees for digital goods
- a marketing guide for paywall design
- a substitute for backend server-to-server transaction validation

---

# 1. Product Type Taxonomy & Classification

Mobile app stores define four distinct product types. Choosing the correct product classification is critical for store review approval and transaction handling logic.

```text
                                IN-APP PURCHASE PRODUCT TYPES
                                              │
       ┌──────────────────────────────┬───────┴──────────────────────┬──────────────────────────────┐
       ▼                              ▼                              ▼                              ▼
[ Auto-Renewable Sub ]       [ Non-Renewing Sub ]         [ Consumable ]               [ Non-Consumable ]
Recurring access to          Access for a fixed           Temporary digital currency,  Permanent feature unlock
features (Weekly, Monthly)   duration without auto-renew  credits, or single-use items or lifetime access
```

| Product Type | Storage Location | Server Validation | Restore Required? | Key Platform APIs |
|---|---|---|---|---|
| **Auto-Renewable Subscription** | Store Server + App Server | Mandatory (Server Notifications v2 / RTDN) | Yes | StoreKit 2 `Product.SubscriptionInfo`, Play Billing 8.0/9.0+ |
| **Non-Renewing Subscription** | App Server Database | Mandatory | Yes | StoreKit 2 `Product.type = .nonRenewable`, Play Billing |
| **Consumable** | App Server Database Balance | Mandatory | No (Single-use) | StoreKit 2 `Product.type = .consumable`, Play Billing `consumeAsync` |
| **Non-Consumable** | Store Server + App Server | Mandatory | Yes (Mandatory UI Button) | StoreKit 2 `Product.type = .nonConsumable`, Play Billing |

---

# 2. Digital Goods vs Physical Goods (Apple Guideline 3.1.1)

Store review teams strictly enforce payment processing boundaries based on product deliverability:

```text
┌────────────────────────────────────────────────────────┐
│             APPLE GUIDELINE 3.1.1 RULE                 │
│                                                        │
│   Digital Goods & Services (Features, Premium Access,   │
│   Subscriptions, Game Items, Digital Content) MUST     │
│   use In-App Purchase (StoreKit 2 / Play Billing).     │
│                                                        │
│   Physical Goods & Real-World Services (E-Commerce,   │
│   Food Delivery, Ridesharing) MUST NOT use IAP.        │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Digital Product ]                [ Physical Product ]
 Use StoreKit 2 / Play Billing       Use Stripe, Braintree,
 Store Fee Applies                   Apple Pay (Physical)
```

---

# 3. StoreKit 2 & Google Play Billing Library 8.0/9.0+ Architecture

Modern mobile commerce relies on native frameworks offering cryptographic transaction verification out of the box:

- **StoreKit 2 (iOS)**: Delivers transactions as cryptographically signed JSON Web Signatures (JWS). Client and server verify Apple signatures directly.
- **Google Play Billing Library 8.0/9.0+ (Android)**: Replaces legacy billing APIs with multi-offer base plans, `BillingProgram` APIs for Billing Choice, and JSON purchase tokens verified via Google Play Developer APIs.

```typescript
// Example: Purchasing a Product with React Native Purchases (RevenueCat)
import Purchases from 'react-native-purchases';

export async function makePurchase(packageToPurchase: PurchasesPackage) {
  try {
    const { customerInfo } = await Purchases.purchasePackage(packageToPurchase);
    if (typeof customerInfo.entitlements.active['pro'] !== 'undefined') {
      // Grant access to Pro entitlement
      return { success: true };
    }
  } catch (e: any) {
    if (!e.userCancelled) {
      console.error('Purchase error:', e.message);
    }
  }
  return { success: false };
}
```

---

# 4. Operational Verification Checklist

- [ ] **Guideline 3.1.1 Compliant**: Digital items use StoreKit 2 / Play Billing 8.0+; physical items use Stripe/Apple Pay.
- [ ] **Product IDs Matching**: Product identifiers in app code match active products in App Store Connect and Google Play Console.
- [ ] **Restore Purchases Active**: "Restore Purchases" button implemented for subscriptions and non-consumables.
- [ ] **Sandbox Tested**: Verified successful purchases in StoreKit Sandbox and Google Play License Testing.
- [ ] **Server Validation Active**: Backend server validates JWS transaction signatures or Play tokens.

---

# 5. Related Documentation

- [Subscriptions Handbook](subscriptions.md) - Subscriptions & auto-renewable groups.
- [Consumables Handbook](consumables.md) - Consumables & balance tracking.
- [Receipt Validation Handbook](receipt-validation.md) - Server-side receipt validation.

---

# 6. Official Sources

- Apple Guideline 3.1.1: https://developer.apple.com/app-store/review/guidelines/#in-app-purchase
- StoreKit 2 Overview: https://developer.apple.com/documentation/storekit
- Google Play Billing Library Documentation: https://developer.android.com/google/play/billing

---

**Last verified:** August 14, 2026
