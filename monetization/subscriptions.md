# Subscription Architecture & Management

This document details the architectural design, subscription group setups, auto-renewable and non-renewing subscription mechanics, base plans, and compliance rules for **Subscriptions** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to structure recurring monetization while ensuring compliance with Apple App Store Review Guidelines (Guideline 3.1.2) and Google Play Subscription policies.

This guide is **not**:

- a marketing copy generator for paywall trial text
- a justification for hiding subscription renewal terms from end users
- a substitute for backend real-time webhook event processors

---

# 1. Subscription Group Architecture (iOS) & Base Plans (Android)

Subscriptions must be organized into logical groups to manage upgrades, downgrades, and crossgrades.

```text
┌────────────────────────────────────────────────────────┐
│             SUBSCRIPTION GROUP: "Pro Pass"             │
│                                                        │
│  Level 1 (Highest): Pro Yearly / Pro Monthly           │
│           ▲                                            │
│           │ (Upgrade / Downgrade)                      │
│           ▼                                            │
│  Level 2 (Basic): Basic Yearly / Basic Monthly         │
└────────────────────────────────────────────────────────┘
```

### Key Differences Between iOS and Android Subscription Modeling

- **iOS Subscription Groups**: A user can only subscribe to **one** active subscription per Subscription Group at a time. Upgrading to a higher level takes effect immediately; downgrading to a lower level takes effect at the next renewal date.
- **Android Base Plans & Offers**: Google Play Billing 8.0/9.0+ defines a single Subscription entity with multiple **Base Plans** (e.g., Monthly Auto-Renew, Yearly Auto-Renew) and attached **Offers** (e.g., 7-day free trial, 50% off for 3 months).

---

# 2. Required Store Paywall Disclosure Rules (Guideline 3.1.2)

Both Apple (Guideline 3.1.2) and Google require explicit disclosures on any paywall displaying subscriptions:

```text
┌────────────────────────────────────────────────────────┐
│             STORE PAYWALL REQUIRED DISCLOSURES         │
│                                                        │
│  - Subscription Title and Duration (e.g., "1 Month")   │
│  - Price and Price Per Unit (e.g., "$9.99/month")      │
│  - Trial Duration (if applicable, e.g., "7-Day Trial") │
│  - Clear Disclosure: Payment charged upon confirmation │
│  - Renewal Disclosure: Auto-renews unless canceled     │
│    at least 24 hours before current period ends.       │
│  - Links to Privacy Policy and Terms of Use (EULA)     │
│  - Ongoing Value Rule: Clear statement of continuous   │
│    dynamic content / service value delivered.          │
└────────────────────────────────────────────────────────┘
```

> **STORE REJECTION RISK**: Omitting functional links to your Privacy Policy and Terms of Use (EULA) on the paywall screen is one of the most common causes of App Store rejections under Guideline 3.1.2.

---

# 3. Code Implementation Example (Subscription Group Upgrade)

```typescript
import Purchases from 'react-native-purchases';

export async function upgradeSubscription(newPackage: PurchasesPackage) {
  try {
    const { customerInfo } = await Purchases.purchasePackage(newPackage);
    // Check if new entitlement is active
    if (customerInfo.entitlements.active['pro_tier']?.productIdentifier === newPackage.product.identifier) {
      return { status: 'UPGRADED' };
    }
  } catch (error: any) {
    if (error.userCancelled) {
      return { status: 'CANCELLED' };
    }
    throw error;
  }
}
```

---

# 4. Operational Verification Checklist

- [ ] **Subscription Groups Organized**: iOS subscription levels configured logically for upgrades/downgrades.
- [ ] **Guideline 3.1.2 Disclosures Complete**: Price, duration, auto-renewal terms, and EULA/Privacy links visible on paywall.
- [ ] **Ongoing Value Rule Satisfied**: Subscription offers continuous dynamic content or services.
- [ ] **Localized Pricing**: Prices rendered dynamically using native store currency strings.
- [ ] **Restore Purchases Tested**: "Restore Purchases" button functional in paywall header/footer.
- [ ] **Webhook Synced**: Server-side notifications handle renewals and cancellations asynchronously.

---

# 5. Related Documentation

- [Subscription Lifecycle Handbook](subscription-lifecycle.md) - Lifecycle states & webhooks.
- [Offers Handbook](offers.md) - Free trials & promo codes.
- [Receipt Validation Handbook](receipt-validation.md) - Server validation.

---

# 6. Official Sources

- Apple Guideline 3.1.2 (Subscriptions): https://developer.apple.com/app-store/review/guidelines/#subscriptions
- Google Play Subscriptions Policy: https://support.google.com/googleplay/android-developer/answer/9858738

---

**Last verified:** August 14, 2026
