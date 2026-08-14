# Mobile Monetization & App Store Commerce

This directory contains production handbook guides, architecture patterns, transaction lifecycle schemas, receipt validation flows, and store review compliance rules for **Mobile App Monetization & In-App Commerce** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**—specifically Apple App Store Review Guidelines (Guidelines 3.1.1 & 3.1.2), StoreKit 2, Google Play Billing Library (PBL 8.0 / 9.0+), and the Google Play Billing Choice Program—it provides exhaustive technical and operational guidance for implementing in-app purchases, subscriptions, paywalls, and backend payment infrastructure.

This guide is **not**:

- an authorization mechanism to bypass Apple or Google store commission fees for digital goods
- a generic marketing pricing strategy guide
- a substitute for backend server-to-server transaction validation and real-time webhook handlers

---

# 1. Architecture of Mobile Monetization

Mobile app monetization bridges client-side UI paywalls with native store payment APIs, server-side receipt validation services, and customer entitlement management systems.

```text
┌────────────────────────────────────────────────────────┐
│               PAYWALL & ENTITLEMENT UI                 │
│  (React Native Paywall Component / Feature Gate)       │
└──────────────────────────┬─────────────────────────────┘
                           │
             [ Trigger In-App Purchase ]
                           │
    ┌──────────────────────┴──────────────────────┐
    ▼                                             ▼
┌────────────────────────┐               ┌────────────────────────┐
│  iOS StoreKit 2 Engine │               │ Play Billing 8+/9+ Engine│
│ (Signed JWS Transaction)               │ (Purchase Token Payload)│
└───────────┬────────────┘               └───────────┬────────────┘
            │                                        │
            └───────────────────┬────────────────────┘
                                │
                                ▼
┌────────────────────────────────────────────────────────┐
│             SERVER-SIDE RECEIPT VALIDATOR              │
│  - Decodes StoreKit 2 JWS / Validates Play Auth Token  │
│  - Verifies Signature against Official Store Keys      │
│  - Listens to App Store Server Notifications v2 & RTDN │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             CUSTOMER ENTITLEMENT SYSTEM                │
│  (Grants Access to Pro Features & Subscriptions)       │
└──────────────────────────┬─────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Purpose & Scope | Key Platform & Store Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, commerce architecture, and universal monetization rules. | High-level commerce flow, 2026 platform rules, and compliance framework. |
| **[in-app-purchases.md](in-app-purchases.md)** | Foundation guide to StoreKit 2 and Google Play Billing 8.0/9.0+ integration. | Apple Guideline 3.1.1 mandatory IAP rules, digital vs physical goods. |
| **[subscriptions.md](subscriptions.md)** | Auto-renewable and non-renewing subscription architecture and group setups. | Subscription groups, base plans, offers, Guideline 3.1.2 paywall disclosures. |
| **[consumables.md](consumables.md)** | Consumable digital products (coins, credits, tokens) and balance tracking. | Server-side balance reconciliation, idempotency keys, and replay defense. |
| **[non-consumables.md](non-consumables.md)** | Permanent feature unlocks and lifetime access products. | Mandatory "Restore Purchases" UI requirement and StoreKit 2 sync. |
| **[subscription-lifecycle.md](subscription-lifecycle.md)** | Subscription state transitions (Grace Period, Account Hold, Paused, Canceled). | App Store Server Notifications v2, Google Play RTDN webhooks. |
| **[offers.md](offers.md)** | Introductory offers, promotional offers, promo codes, and win-back discounts. | Custom codes, eligibility rules, and StoreKit 2 ECDSA offer signatures. |
| **[pricing.md](pricing.md)** | App Store price tiers, Google Play regional pricing, taxes, and price increases. | Automatic price increase policies, user consent rules, and localization. |
| **[receipt-validation.md](receipt-validation.md)** | Server-side transaction validation and JWS signature verification. | Anti-fraud security, StoreKit 2 JWS verification, Google Developer API. |
| **[entitlements.md](entitlements.md)** | Client & server entitlement management, paywalls, and feature gating. | RevenueCat / custom backend entitlement mapping, paywall UX safety. |

---

# 3. Universal Monetization Rules

All commerce implementations in this playbook must adhere to five mandatory engineering rules:

```text
1. Apple Guideline 3.1.1 & 3.1.2 Strict Compliance
   All digital content, features, subscriptions, and virtual currencies sold within an app
   MUST use Apple In-App Purchase (StoreKit 2) and Google Play Billing (PBL 8.0/9.0+).
   Direct credit card processing (Stripe, Braintree) is strictly forbidden for digital goods.

2. Mandatory Paywall Disclosure Rules (Guideline 3.1.2)
   Paywalls MUST clearly disclose subscription title, length, localized price, auto-renewal
   terms, and functional links to the Privacy Policy and Terms of Use (EULA).

3. Mandatory "Restore Purchases" UI Requirement
   Apps offering non-consumable purchases or auto-renewable subscriptions MUST include a prominent,
   functional "Restore Purchases" button in the paywall and settings interface.

4. Real-Time Webhook Synchronization
   Monetization systems MUST process App Store Server Notifications v2 and Google Play Real-Time
   Developer Notifications (RTDN) to handle subscription cancellations, refunds, grace periods,
   and renewals asynchronously.

5. Opaque Price Localization
   Hardcoding currency symbols (e.g., "$9.99") in app code is strictly prohibited. Prices MUST
   be rendered dynamically using localized price strings formatted by native store APIs.
```

---

# 4. Related Repository Documentation

- [Payments Integration Guide](../integrations/payments/README.md) - Payments SDK integrations.
- [Mobile Release Lifecycle](../foundations/mobile-release-lifecycle.md) - Lifecycle release phases.
- [Store Metadata Workflow](../ai/workflows/metadata-generation.md) - Metadata generation.
- [Rejection Analysis Prompt](../ai/prompts/rejection-analysis.md) - Guideline 3.1.1 rejections.

---

# 5. Official Sources

- Apple App Store Review Guidelines (Guidelines 3.1.1 & 3.1.2): https://developer.apple.com/app-store/review/guidelines/#in-app-purchase
- StoreKit 2 Documentation: https://developer.apple.com/documentation/storekit
- Google Play Billing Library Documentation: https://developer.android.com/google/play/billing
- Google Play Billing Choice Program: https://developer.android.com/google/play/billing/billing-choice
- App Store Server Notifications v2: https://developer.apple.com/documentation/appstoreservernotifications

---

**Last verified:** August 14, 2026
