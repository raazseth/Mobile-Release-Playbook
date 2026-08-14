# Subscription Lifecycle & Webhook Architecture

This document details the state machine transitions, event webhooks (App Store Server Notifications v2 for iOS, Google Play Real-Time Developer Notifications - RTDN for Android), Grace Period, Account Hold, and billing recovery mechanics for **Subscriptions** in Expo and React Native applications.

It specifies how to handle asynchronous subscription state changes on server backends to maintain accurate customer entitlement states.

This guide is **not**:

- an in-app UI paywall guide (see [subscriptions.md](subscriptions.md))
- an optional feature (server-to-server webhook handling is mandatory for subscription management)
- a polling-only architecture (relying exclusively on client polling creates missed renewals and billing gaps)

---

# 1. Subscription State Machine Taxonomy

A subscription moves through distinct lifecycle states throughout its lifespan. Server backends must maintain a state machine tracking user subscription status:

```text
               ┌─────────────────────────────────────────┐
               │              SUBSCRIBED                 │
               │   (Active Paid or Trial Subscription)   │
               └────────────────────┬────────────────────┘
                                    │
           ┌────────────────────────┼────────────────────────┐
           ▼                        ▼                        ▼
┌────────────────────┐   ┌────────────────────┐   ┌────────────────────┐
│    GRACE PERIOD    │   │    ACCOUNT HOLD    │   │     CANCELED       │
│ Billing retry;     │   │ Payment failed;    │   │ Auto-renew off;    │
│ Access MAINTAINED  │   │ Access REVOKED     │   │ Active until end   │
└──────────┬─────────┘   └──────────┬─────────┘   └──────────┬─────────┘
           │                        │                        │
           ▼                        ▼                        ▼
┌────────────────────────────────────────────────────────┐
│                        EXPIRED                         │
│       Access REVOKED; Eligible for Win-Back Offer      │
└────────────────────────────────────────────────────────┘
```

---

# 2. State Transition Definitions & Entitlement Rules

| Subscription State | Trigger Event | Access Status | Required Action |
|---|---|---|---|
| **Subscribed (Active)** | Initial purchase or successful renewal | **GRANT ACCESS** | Maintain full feature access. |
| **Grace Period** | Billing failure on renewal, but store in grace period (e.g., 6 or 16 days) | **GRANT ACCESS** | Maintain access; display soft billing warning UI. |
| **Account Hold** | Grace period expires without payment recovery | **REVOKE ACCESS** | Revoke access; display payment update prompt. |
| **Canceled (Pending Expiry)** | User turns off auto-renew in store settings | **GRANT ACCESS** | Maintain access until `expires_date`; display win-back offer. |
| **Expired** | Expiration date reached without renewal | **REVOKE ACCESS** | Revoke access; move to free tier. |
| **Refunded** | Apple / Google customer support approves refund | **REVOKE ACCESS** | Immediately revoke access upon receiving refund webhook. |

---

# 3. Webhook Architecture: App Store Notifications v2 & Play RTDN

Server backends MUST implement webhook listeners to process real-time notifications from Apple and Google.

```text
┌────────────────────────────────────────────────────────┐
│             STORE WEBHOOK SERVER ENDPOINT              │
│       `POST https://api.example.com/webhooks/apple`    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             DECODE & VERIFY SIGNED PAYLOAD             │
│  - Apple: Decode Signed Payload JWS (`notificationUUID`)│
│  - Google: Decode Cloud Pub/Sub base64 JSON payload    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            UPDATE CUSTOMER DATABASE & STATE            │
│  - Update `sub_status`, `expires_at`, and entitlements │
└────────────────────────────────────────────────────────┘
```

### Apple App Store Server Notifications v2 Key Notification Types

- `SUBSCRIBED`: New subscription or upgrade.
- `DID_RENEW`: Successful auto-renewal.
- `DID_FAIL_TO_RENEW`: Billing failure; check `is_in_billing_retry_period`.
- `GRACE_PERIOD_EXPIRED`: Grace period ended without payment recovery.
- `REFUND`: Customer support granted a refund; revoke entitlement immediately.
- `REVOKE`: Family sharing entitlement revoked.

---

# 4. Operational Verification Checklist

- [ ] **Webhook Endpoint Active**: HTTPS webhook endpoints deployed with valid SSL certificates.
- [ ] **Apple JWS Signature Verified**: App Store Notifications v2 JWS signatures verified using Apple Root Certificates.
- [ ] **Google Pub/Sub Authenticated**: Google Play RTDN messages authenticated via GCP service account tokens.
- [ ] **Grace Period Handled**: App maintains entitlement access during active grace period.
- [ ] **Refund Revocation Immediate**: Entitlements are revoked instantly upon receiving `REFUND` notification.

---

# 5. Related Documentation

- [Subscriptions Handbook](subscriptions.md) - Subscriptions setup & groups.
- [Receipt Validation Handbook](receipt-validation.md) - JWS validation.
- [Offers Handbook](offers.md) - Win-back offers & trials.

---

# 6. Official Sources

- Apple App Store Server Notifications v2: https://developer.apple.com/documentation/appstoreservernotifications
- Google Play Real-Time Developer Notifications (RTDN): https://developer.android.com/google/play/billing/rtdn

---

**Last verified:** August 14, 2026
