# Customer Entitlement Management & Paywall Architecture

This document details the architectural design, entitlement abstraction layers, paywall presentation patterns, feature gating mechanisms, and integration rules (RevenueCat, custom backend entitlement services) for **Customer Entitlements** in Expo and React Native applications.

It specifies how to map raw in-app purchase products to abstract feature permissions (e.g., `pro_access`, `export_pdf`, `unlimited_cloud_sync`) while ensuring seamless offline resilience and store compliance.

This guide is **not**:

- a hardcoded feature check script (entitlement logic must be decoupled from raw store product IDs)
- a marketing paywall A/B testing guide
- a substitute for backend server entitlement synchronization

---

# 1. Architecture of Entitlement Abstraction

Mobile applications should never check raw product IDs (e.g., `if (productId === 'com.example.monthly_999')`) throughout frontend screen components. Doing so creates brittle code that breaks whenever price tiers or product configurations change.

```text
┌────────────────────────────────────────────────────────┐
│           RAW STORE PURCHASES & SUBSCRIPTIONS          │
│  (StoreKit 2 / Google Play Billing Product IDs)        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            ENTITLEMENT ABSTRACTION MAPPING             │
│  - Maps `com.example.pro_monthly`  ──→  `pro_access`   │
│  - Maps `com.example.pro_yearly`   ──→  `pro_access`   │
│  - Maps `com.example.lifetime_pro` ──→  `pro_access`   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            FRONTEND UI FEATURE GATE COMPONENT          │
│  `if (hasEntitlement('pro_access')) { renderFeature() }`│
└────────────────────────────────────────────────────────┘
```

---

# 2. Entitlement Mapping Schema (Custom Backend or RevenueCat)

Entitlements represent high-level feature permissions granted to a user account.

```json
{
  "user_id": "usr_98412abcdef349",
  "entitlements": {
    "pro_access": {
      "is_active": true,
      "product_identifier": "com.example.pro_yearly",
      "purchase_date": "2026-01-15T10:00:00Z",
      "expiration_date": "2027-01-15T10:00:00Z",
      "will_renew": true,
      "is_sandbox": false
    },
    "export_4k_video": {
      "is_active": true,
      "product_identifier": "com.example.lifetime_pro",
      "expiration_date": null
    }
  }
}
```

---

# 3. React Native Feature Gate Pattern

Create reusable React hooks or UI components that query active customer entitlements:

```typescript
// Custom React Hook for Entitlement Check
import { useCustomerInfo } from './useCustomerInfo';

export function useEntitlement(entitlementIdentifier: string): boolean {
  const { customerInfo, isLoading } = useCustomerInfo();

  if (isLoading || !customerInfo) {
    return false;
  }

  return customerInfo.entitlements.active[entitlementIdentifier]?.isActive ?? false;
}

// UI Feature Gate Component Usage
export function FeatureContainer({ children }: { children: React.ReactNode }) {
  const isPro = useEntitlement('pro_access');

  if (!isPro) {
    return <PaywallBanner promptText="Upgrade to Pro to unlock advanced analytics." />;
  }

  return <>{children}</>;
}
```

---

# 4. Offline Resilience & Cache Management

Entitlement status should be cached securely on the mobile device to enable offline access for active subscribers:

- **Cache Encrypted Entitlements**: Store active entitlement status in `expo-secure-store` or encrypted local storage.
- **Graceful Expiration Check**: Allow cached access when offline if the current timestamp is before `expiration_date`.
- **Refresh on Reconnect**: Automatically re-query backend/store entitlement state when device regains network connectivity.

---

# 5. Operational Verification Checklist

- [ ] **Decoupled Feature Gates**: UI code checks abstract entitlement keys (`pro_access`), never raw product IDs.
- [ ] **Offline Access Active**: Active subscribers retain feature access when launching app without internet connection.
- [ ] **Paywall Fallback Ready**: Paywall displays cached or default price strings if network fails.
- [ ] **Restore Purchases Linked**: Paywall component includes a functional "Restore Purchases" button.
- [ ] **Server State Synced**: Backend database maps active customer entitlements to user accounts.

---

# 6. Related Documentation

- [In-App Purchases Handbook](in-app-purchases.md) - Product taxonomy.
- [Subscriptions Handbook](subscriptions.md) - Subscriptions setup.
- [Non-Consumables Handbook](non-consumables.md) - Lifetime access.

---

# 7. Official Sources

- RevenueCat Entitlements Guide: https://www.revenuecat.com/docs/entitlements
- Apple StoreKit 2 Transaction Verification: https://developer.apple.com/documentation/storekit/transaction

---

**Last verified:** August 14, 2026
