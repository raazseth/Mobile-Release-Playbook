# Pricing Tiers, Regional Pricing & Price Increases

This document details the architectural design, price tier structures, regional currency localization, tax settings, and compliance rules for **Pricing & Price Increases** in Expo and React Native applications.

It specifies how to manage global app store product pricing while ensuring compliance with Apple App Store Review Guidelines (Guideline 3.1.2) and Google Play Price Change policies.

This guide is **not**:

- an authorization mechanism to hardcode static currency strings in frontend UI code
- an automated price changer (price changes must be configured via App Store Connect and Google Play Console)
- a substitute for user price increase consent notifications

---

# 1. App Store Price Structures & Regional Currency Localization

Mobile app stores manage global pricing across hundreds of storefront currencies, automatically adjusting for local Value Added Tax (VAT) and exchange rate fluctuations.

```text
┌────────────────────────────────────────────────────────┐
│             STOREFRONT CURRENCY LOCALIZATION           │
│                                                        │
│  - US Storefront: "$9.99"                              │
│  - Eurozone Storefront: "9.99 €"                       │
│  - Japan Storefront: "¥1,500"                          │
│  - UK Storefront: "£8.99"                              │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Render Localized Price String directly from Store SDK ]
```

### The Cardinal Rule of Price Rendering

> **NEVER hardcode currency symbols or prices in JavaScript source code.** Always format paywall price labels using the `localizedPrice` or `priceString` properties returned by StoreKit 2 or Google Play Billing APIs.

```typescript
// SAFE: Rendering Localized Price from Product Store Object
import { Product } from 'react-native-purchases';

export function PaywallPriceLabel({ product }: { product: Product }) {
  // Renders "$9.99", "9,99 €", or "¥1,500" automatically based on user's App Store account
  return <Text>{product.priceString} / month</Text>;
}
```

---

# 2. Subscription Price Increase Policies

When increasing the price of an active auto-renewable subscription, both Apple and Google enforce strict customer notification rules to prevent unexpected billing charges.

```text
┌────────────────────────────────────────────────────────┐
│              PRICE INCREASE COMPLIANCE RULES           │
│                                                        │
│  Apple App Store:                                      │
│  - Notification Period: Minimum 30 days notice.        │
│  - Automatic Renewal: Allowed for small price increases│
│    (under specific limits) with notice.                │
│  - Explicit Opt-In Consent: Required for large price   │
│    increases; fails to renew if user does not consent. │
│                                                        │
│  Google Play Store:                                    │
│  - Legacy Cohort: Keep existing users on legacy price. │
│  - Price Opt-In: Requires explicit user consent for    │
│    increases above threshold; cancels if un-consented. │
└────────────────────────────────────────────────────────┘
```

---

# 3. Tax & Regulatory Declarations

When setting up product pricing in store consoles:

- **Apple App Store Connect**: Select appropriate Tax Category (e.g., Digital Educational Material, Standard Digital Audio/Video, General Digital Goods). Apple handles VAT collection and remittance in covered territories.
- **Google Play Console**: Declare whether prices are tax-inclusive or tax-exclusive based on country tax laws.

---

# 4. Operational Verification Checklist

- [ ] **Dynamic Price Rendering**: Paywall displays `product.priceString` from store API; zero hardcoded currency symbols.
- [ ] **Regional Pricing Verified**: Tested paywall layout with long localized currency strings (e.g., "1.499,00 TL").
- [ ] **Price Increase Compliance**: Price increase strategy uses grandfathering or complies with store notification limits.
- [ ] **Tax Category Assigned**: Correct digital goods tax category configured in App Store Connect and Google Play Console.
- [ ] **Human Sign-Off**: Global price changes authorized by a human maintainer before updating store tiers.

---

# 5. Related Documentation

- [Subscriptions Handbook](subscriptions.md) - Subscriptions setup.
- [Offers Handbook](offers.md) - Promotional offers & trials.
- [Store Metadata Workflow](../ai/workflows/metadata-generation.md) - Metadata setup.

---

# 6. Official Sources

- Apple App Store Price Tier Updates: https://developer.apple.com/news/?id=09122022b
- Google Play Subscription Price Changes: https://developer.android.com/google/play/billing/price-changes

---

**Last verified:** August 14, 2026
