# Non-Consumable In-App Purchases & Restoration

This document details the architectural design, permanent feature unlock handling, StoreKit 2 transaction history synchronization, and mandatory "Restore Purchases" UI requirements for **Non-Consumable In-App Purchases** (lifetime access, pro feature unlocks, ad removal, offline pack downloads) in Expo and React Native applications.

It specifies how to manage non-expiring digital items while ensuring strict compliance with Apple App Store Review Guidelines (Guideline 3.1.1).

This guide is **not**:

- a consumable product guide (see [consumables.md](consumables.md))
- a subscription guide (see [subscriptions.md](subscriptions.md))
- an optional requirement ("Restore Purchases" is mandatory for non-consumable products)

---

# 1. Non-Consumable Architecture & Lifecycle

Non-consumable purchases are bought once and associated permanently with the user's Apple ID or Google Account.

```text
[ User Purchases "Lifetime Pro Unlock" ]
                   │
                   ▼
[ StoreKit 2 / Google Play Billing Processing ]
                   │
                   ▼
┌────────────────────────────────────────────────────────┐
│            PERMANENT ENTITLEMENT RECORDING             │
│  - iOS: Recorded permanently in Apple ID purchase history│
│  - Android: Recorded in Google Account purchases      │
│  - Backend: Mapped to User Account ID                  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Grant Permanent Access to Feature (No Expiration) ]
```

---

# 2. Mandatory "Restore Purchases" UI Requirement

Apple Guideline 3.1.1 strictly mandates that any app offering non-consumable products or subscriptions MUST provide a prominent, accessible "Restore Purchases" mechanism.

```text
┌────────────────────────────────────────────────────────┐
│              MANDATORY RESTORE PURCHASES UI            │
│                                                        │
│   [ Unlock Lifetime Pro - $49.99 ]                      │
│                                                        │
│   Already purchased? [ Restore Purchases ]             │
└────────────────────────────────────────────────────────┘
```

### Common App Store Rejection Triggers

- **Hidden Restore Button**: Placing the "Restore Purchases" button deep inside hidden settings sub-menus.
- **Silent Failure**: Failing to provide user feedback (alert or toast) if the restore process completes without finding active purchases.
- **Authentication Loop**: Requiring users to enter passwords multiple times during the restoration flow.

---

# 3. StoreKit 2 Transaction History Sync (`AppStore.sync()`)

On iOS 15+, StoreKit 2 maintains a local, cryptographically signed record of all non-consumable purchases in `Transaction.currentEntitlements`.

```typescript
// React Native Restoration Example (RevenueCat)
import Purchases from 'react-native-purchases';
import { Alert } from 'react-native';

export async function handleRestorePurchases() {
  try {
    const customerInfo = await Purchases.restorePurchases();
    if (typeof customerInfo.entitlements.active['lifetime_pro'] !== 'undefined') {
      Alert.alert('Success', 'Your Lifetime Pro purchase has been restored!');
    } else {
      Alert.alert('No Purchases Found', 'No active lifetime purchases were found for this account.');
    }
  } catch (e: any) {
    Alert.alert('Error', e.message);
  }
}
```

---

# 4. Operational Verification Checklist

- [ ] **Prominent Restore Button**: "Restore Purchases" button clearly visible on paywall and settings screens.
- [ ] **User Feedback Provided**: Clear success/info alerts displayed after restoration completes.
- [ ] **Cross-Device Sync Active**: Non-consumables restore successfully when user logs in on a new device.
- [ ] **No Expiration Date**: Entitlements mapped to non-consumables remain active indefinitely.
- [ ] **Server Entitlement Synced**: Backend database updates account status upon receipt validation.

---

# 5. Related Documentation

- [In-App Purchases Architecture](in-app-purchases.md) - Product taxonomy.
- [Entitlements Handbook](entitlements.md) - Paywalls & feature gates.
- [Receipt Validation Handbook](receipt-validation.md) - Server verification.

---

# 6. Official Sources

- Apple Guideline 3.1.1: https://developer.apple.com/app-store/review/guidelines/#in-app-purchase
- StoreKit 2 Current Entitlements: https://developer.apple.com/documentation/storekit/transaction/3856525-currententitlements

---

**Last verified:** August 14, 2026

