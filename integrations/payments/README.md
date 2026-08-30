# Payments & In-App Purchase Integration

This guide details the integration architecture, StoreKit 2, Google Play Billing 7.0+, RevenueCat, Stripe SDK, and store review compliance rules for **Payments & In-App Purchases (IAP)** in React Native and Expo applications.

It specifies how to implement digital product purchases and subscriptions while ensuring strict compliance with Apple App Store Review Guidelines (Guideline 3.1.1) and Google Play Payments policies.

This guide is **not**:

- an authorization mechanism to bypass Apple or Google in-app purchase fees for digital goods
- a backend server tutorial (server-side receipt verification and webhooks are mandatory)
- a substitute for configuring IAP products in App Store Connect and Google Play Console

---

# 1. Integration Architecture & Digital vs Physical Product Rules

The choice of payment engine is strictly governed by store policy based on the nature of the product sold:

```text
                                  PRODUCT CLASSIFICATION
                                             │
      ┌──────────────────────────────────────┴──────────────────────────────────────┐
      ▼                                                                             ▼
[ DIGITAL GOODS / SERVICES ]                                               [ PHYSICAL GOODS / SERVICES ]
- In-app features, subscriptions, digital content                          - Physical goods, ridesharing, food delivery
- MUST use Apple IAP (StoreKit 2) & Google Play Billing                    - MUST use Credit Card SDK (Stripe, Braintree, Apple Pay)
- Apple Guideline 3.1.1 Mandatory                                          - Direct credit card processing permitted
```

---

# 2. Apple Guideline 3.1.1 & StoreKit 2 Integration

Apple Guideline 3.1.1 strictly mandates In-App Purchases for digital content:

- **Digital Products**: Subscriptions, unlockable features, virtual currency, game items MUST use StoreKit 2.
- **No External Payment Buttons**: You MUST NOT include buttons, external links, or calls-to-action directing users to purchase digital items on a web browser unless operating under an explicit Apple-approved Reader App exemption or Regional Multi-Payment mandate.

```json
{
  "expo": {
    "plugins": [
      [
        "react-native-purchases",
        {
          "enableAmazon": false
        }
      ]
    ]
  }
}
```

---

# 3. Server-Side Receipt Verification & Webhooks

Client-side transaction validation alone is vulnerable to receipt spoofing. Mobile payment architectures MUST implement server-to-server transaction validation:

```text
[ Client Device ] ──( Purchases Product via StoreKit 2 )──→ [ Apple App Store ]
        │                                                         │
        ▼ (Sends Signed Transaction JWS Token)                    ▼ (Sends Real-Time Server Notification)
[ Backend API Server ] ◄──────────────────────────────────────────┘
        │
        ▼ (Validates Signature with Apple Public Keys)
[ Grant Entitlement to User Account ]
```

- **Apple Server Notifications**: Configure App Store Server Notifications v2 webhook endpoints in App Store Connect.
- **Google Play Real-Time Developer Notifications (RTDN)**: Configure Cloud Pub/Sub topics for Google Play Billing subscription events.

---

# 4. Physical Goods & Stripe SDK Integration

For physical goods (e.g., e-commerce physical items, food delivery, physical service bookings):

- Integrate Stripe SDK or Braintree for direct credit card processing and Apple Pay / Google Pay.
- StoreKit 2 / Google Play Billing MUST NOT be used for physical goods.

---

# 5. Operational Verification Checklist

- [ ] **Guideline 3.1.1 Compliant**: Digital goods use StoreKit 2 and Google Play Billing 7.0+; physical goods use Stripe/Apple Pay.
- [ ] **Server-to-Server Validation**: Receipt validation implemented on backend server via App Store Server Notifications v2 and Play RTDN.
- [ ] **Restore Purchases Implemented**: "Restore Purchases" button prominently displayed in app purchase UI.
- [ ] **Products Configured in Consoles**: Product identifiers in code match active products in App Store Connect and Google Play Console.
- [ ] **Sandbox Tested**: Transactions verified successfully in StoreKit Sandbox and Google Play License Testing tracks.

---

# 6. Related Documentation

- [Rejection Analysis Prompt](../../ai/prompts/rejection-analysis.md) - Handling Guideline 3.1.1 rejections.
- [Metadata Agent](../../ai/agents/metadata-agent.md) - Metadata and store review safety.
- [Release Audit Workflow](../../ai/workflows/release-audit.md) - Pre-release audits.

---

# 7. Official Sources

- Apple Guideline 3.1.1 (In-App Purchase): https://developer.apple.com/app-store/review/guidelines/#in-app-purchase
- StoreKit 2 Documentation: https://developer.apple.com/documentation/storekit
- Google Play Billing Library: https://developer.android.com/google/play/billing

---

**Last verified:** August 13, 2026

