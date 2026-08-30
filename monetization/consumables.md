# Consumable In-App Purchases & Balance Tracking

This document details the architectural design, balance reconciliation, idempotency handling, and anti-fraud protocols for **Consumable In-App Purchases** (coins, gems, credits, AI tokens, single-use passes) in Expo and React Native applications.

It specifies how to handle temporary digital items that can be purchased repeatedly while ensuring robust server-side transaction accounting and StoreKit 2 / Google Play Billing consumption mechanisms.

This guide is **not**:

- an authorization mechanism to store user coin balances in un-encrypted local storage (`AsyncStorage`)
- a restore-purchases guide (consumables are single-use and cannot be restored via store APIs)
- a substitute for server-side transaction idempotency keys

---

# 1. Consumable Purchase Lifecycle

Unlike non-consumables or subscriptions, consumable purchases must be **consumed** (acknowledged and credited) before the user can purchase the same item again.

```text
[ User Purchases 100 Gems ]
            │
            ▼
[ Native Store API Processing (StoreKit 2 / Play Billing) ]
            │
            ▼
┌────────────────────────────────────────────────────────┐
│             SERVER-SIDE BALANCE RECONCILIATION         │
│  - Verify Transaction JWS / Purchase Token             │
│  - Check Idempotency Key (Has transaction ID been processed?)│
│  - Add 100 Gems to User Database Account Balance      │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             CONSUMPTION ACKNOWLEDGEMENT                │
│  - iOS: Finish Transaction (`transaction.finish()`)    │
│  - Android: Consume Purchase (`consumeAsync()`)        │
└────────────────────────────────────────────────────────┘
```

---

# 2. Server-Side Idempotency & Replay Defense

Because mobile network connections can drop immediately after a purchase completes on the device, the client app may retry sending the purchase payload to your server multiple times.

```sql
-- Database Transaction Ledger Schema
CREATE TABLE user_consumable_ledger (
    transaction_id VARCHAR(255) PRIMARY KEY, -- Store Original Transaction ID
    user_id VARCHAR(255) NOT NULL,
    product_id VARCHAR(255) NOT NULL,
    quantity INT NOT NULL,
    processed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Idempotency Logic Rule

When your backend receives a transaction verification request:

1. Check if `transaction_id` already exists in `user_consumable_ledger`.
2. **If Exists**: Do NOT credit coins again. Return `HTTP 200 OK` with current user balance.
3. **If New**: Credit coins in an atomic database transaction, record `transaction_id`, and return updated balance.

---

# 3. Android Consumption Requirement (`consumeAsync`)

On Android, if a consumable purchase is not explicitly consumed within **3 days**, Google Play automatically refunds the purchase and revokes the transaction.

```typescript
// React Native Google Play Consumption Example
import RNIap from 'react-native-iap';

export async function processAndroidConsumable(purchase: RNIap.Purchase) {
  // 1. Send purchase.purchaseToken to backend for validation & balance credit
  const serverResult = await creditUserBalanceOnBackend(purchase.purchaseToken);

  if (serverResult.success) {
    // 2. Consume the purchase on Google Play so it can be bought again
    await RNIap.consumePurchaseAndroid(purchase.purchaseToken);
  }
}
```

---

# 4. Operational Verification Checklist

- [ ] **Server Ledger Idempotency**: Backend uses `transaction_id` primary key constraints to prevent double-crediting.
- [ ] **Android `consumeAsync` Active**: Android consumable purchases are explicitly consumed post-validation.
- [ ] **StoreKit 2 `finish()` Called**: iOS transactions are finished only after backend verification succeeds.
- [ ] **No Local Balance Mutation**: Coin/credit balances are fetched from the server database, never mutated locally in JS state.
- [ ] **Offline Recovery Tested**: Unfinished transactions recover gracefully upon next app launch.

---

# 5. Related Documentation

- [In-App Purchases Architecture](in-app-purchases.md) - Product type taxonomy.
- [Receipt Validation Handbook](receipt-validation.md) - Server verification.
- [Non-Consumables Handbook](non-consumables.md) - Permanent unlocks.

---

# 6. Official Sources

- Apple StoreKit 2 Finishing Transactions: https://developer.apple.com/documentation/storekit/transaction/3749694-finish
- Google Play Billing Consumables: https://developer.android.com/google/play/billing/integrate#consume

---

**Last verified:** August 14, 2026

