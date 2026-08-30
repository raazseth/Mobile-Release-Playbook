# Server-Side Receipt Validation & Transaction Verification

This document details the architectural design, cryptographic signature verification (StoreKit 2 Signed Transaction JWS), Google Play Developer API verification, anti-fraud security, and implementation rules for **Server-Side Receipt Validation** in Expo and React Native applications.

It specifies how to validate purchase receipts server-side to prevent transaction spoofing, man-in-the-middle attacks, and unauthorized feature unlocking.

This guide is **not**:

- an authorization mechanism to validate transactions exclusively on the mobile client
- a guide to deprecated legacy Apple `/verifyReceipt` API endpoints (migrated to StoreKit 2 JWS / App Store Server API)
- a substitute for verifying cryptographic SSL certificates on server connections

---

# 1. Integration Architecture & Verification Pipeline

Relying on mobile client devices to validate purchases creates severe security vulnerabilities. Malicious users can intercept client network responses or mock native store frameworks to forge purchase success payloads.

```text
[ Client Device Completes Purchase ]
                 │
                 ▼ (Sends Signed JWS / Purchase Token to App Server)
┌────────────────────────────────────────────────────────┐
│             SERVER-SIDE VALIDATION PIPELINE            │
│                                                        │
│  - iOS: Decode StoreKit 2 Signed Transaction JWS       │
│  - Android: Call Google Play Developer API endpoint    │
│  - Verify Signature using Official Apple/Google Public Keys
│  - Check Bundle ID, Environment, and Product ID        │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Signature Validated ]            [ Invalid / Spoofed ]
 Record Transaction in Database     Reject Request & Return
 & Grant Entitlement                 HTTP 400 Bad Request
```

---

# 2. StoreKit 2 Cryptographic Signed Transaction JWS (iOS)

On iOS 15+, StoreKit 2 returns every transaction as a compact **JSON Web Signature (JWS)** token. The payload contains purchase fields signed by Apple's Root Certificate Authority.

### StoreKit 2 JWS Header & Payload Structure

```json
// StoreKit 2 Decoded JWS Header
{
  "alg": "ES256",
  "x5c": ["MIIEMjCCAxqgAwIBAgII...", "MIIB..."] // Certificate chain
}

// StoreKit 2 Decoded JWS Payload
{
  "transactionId": "2000000123456789",
  "originalTransactionId": "1000000987654321",
  "bundleId": "com.example.mobileapp",
  "productId": "com.example.pro_monthly",
  "purchaseDate": 1723636800000,
  "expiresDate": 1726315200000,
  "quantity": 1,
  "type": "Auto-Renewable Subscription",
  "environment": "Production"
}
```

### Server Validation Protocol for StoreKit 2

1. **Verify Certificate Chain**: Parse the `x5c` certificate header and verify the certificate chain traces back to the official **Apple Root CA - G2**.
2. **Verify Signature**: Check that the ECDSA P-256 (`ES256`) signature is valid.
3. **Verify Bundle ID**: Ensure `bundleId` matches your app's exact bundle identifier (`com.example.mobileapp`).
4. **Verify Environment**: Verify `environment` matches `Production` (or `Sandbox` in test environments).

---

# 3. Google Play Developer API Validation (Android)

For Android purchases, the mobile client receives a `purchaseToken`. The app backend server calls the **Google Play Developer API**:

```text
GET https://androidpublisher.googleapis.com/androidpublisher/v3/applications/{packageName}/purchases/subscriptionsv2/tokens/{token}
```

### Required Server Headers & Authentication

- Authenticated via Google OAuth 2.0 Access Token using a Google Cloud Service Account JSON key.
- Key permissions: Granted `View Financial Data` in Google Play Console.

---

# 4. Anti-Fraud & Replay Prevention Rules

1. **Check Transaction Replay**: Store `transactionId` in your server database. If a request attempts to reuse an existing `transactionId` for a different user account, reject the attempt as a replay attack.
2. **Check Shared Secret / App ID**: Ensure the purchase payload belongs strictly to your application bundle ID.
3. **Monitor Refund Status**: Listen to real-time store webhooks (`REFUND` event) to revoke entitlements immediately when a refund is issued.

---

# 5. Operational Verification Checklist

- [ ] **No Client-Only Validation**: Purchase validation logic resides 100% on a secure server backend.
- [ ] **StoreKit 2 JWS Verified**: Server decodes and verifies StoreKit 2 JWS signatures against Apple Root CA.
- [ ] **Google Play API Authenticated**: Server calls Google Play Developer API via service account credentials.
- [ ] **Replay Protection Active**: `transactionId` primary key constraints prevent token reuse across accounts.
- [ ] **Environment Checked**: Server rejects `Sandbox` receipts sent to production endpoints.

---

# 6. Related Documentation

- [In-App Purchases Handbook](in-app-purchases.md) - Product taxonomy.
- [Subscription Lifecycle Handbook](subscription-lifecycle.md) - Webhook state machine.
- [Secret Protection](../ai/security/secret-protection.md) - Masking service account keys.

---

# 7. Official Sources

- Apple StoreKit 2 SignedData Verification: https://developer.apple.com/documentation/storekit/signeddata
- Google Play Developer API (Purchases.subscriptionsv2): https://developers.google.com/android-publisher/api-ref/rest/v3/purchases.subscriptionsv2

---

**Last verified:** August 14, 2026

