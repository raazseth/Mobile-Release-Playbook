# Apple Developer Program Enrollment & Administration

This document details the enrollment procedure, Account Holder setup, D-U-N-S® business identity verification, two-factor authentication (2FA) enforcement, and annual renewal management for the **Apple Developer Program**.

Engineered in alignment with **2026 platform requirements**, it specifies how to set up and maintain an Apple Developer Organization account ($99 USD/year) to enable app publishing, TestFlight distribution, Apple Push Notification service (APNs), and StoreKit 2 monetization.

This guide is **not**:

- an App Store Connect portal management guide (see [app-store-connect.md](app-store-connect.md))
- a guide to signing certificate generation (see [signing/](../../signing/README.md))
- a substitute for verifying official Apple legal entity documentation

---

# 1. Apple Developer Program Structure & Fees

The Apple Developer Program grants access to iOS, iPadOS, macOS, watchOS, and tvOS SDKs, beta software, App Store Connect, and native capabilities (Associated Domains, Sign in with Apple, HealthKit, Push Notifications).

```text
┌────────────────────────────────────────────────────────┐
│               APPLE DEVELOPER PROGRAM                  │
│                                                        │
│  - Annual Membership Fee: $99 USD / year (recurring)   │
│  - Account Type: Organization (Verified Legal Entity)  │
│  - Enrollment Requirements: D-U-N-S® Number + 2FA      │
│  - Authority: Account Holder (Legal Binding Authority) │
└──────────────────────────┬─────────────────────────────┘
                           │
    ┌──────────────────────┴──────────────────────┐
    ▼                                             ▼
┌────────────────────────┐               ┌────────────────────────┐
│ App Store Distribution │               │ Native Capabilities    │
│ (TestFlight & Store)   │               │ (APNs, Push, IAP, AASA)│
└────────────────────────┘               └────────────────────────┘
```

---

# 2. Prerequisites for Organization Enrollment

Before initiating Apple Developer Program enrollment for an organization:

1. **Corporate Apple Account with 2FA**: Create an Apple Account using a corporate email address (e.g., `apple-developer@company.com`). Enable Two-Factor Authentication (2FA) using hardware security keys or corporate Apple devices.
2. **D-U-N-S® Number**: Obtain a valid 9-digit D-U-N-S Number from Dun & Bradstreet representing your legal business entity (e.g., Corporation, LLC). DBAs, fictitious names, trade names, and branch offices are **strictly rejected** by Apple.
3. **Legal Entity Status**: The company must be a recognized legal entity capable of entering into binding contracts with Apple.
4. **Legal Binding Authority**: The person completing enrollment must be the owner/founder, executive officer, or have explicit legal authority granted by an executive. Apple conducts phone calls to verify authority.
5. **Public Corporate Website**: The company must maintain an active, publicly accessible website hosted on a domain associated with the company name.

---

# 3. Enrollment Verification Flow

```text
[ Submit Enrollment Request via Developer App / Web ]
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│              DUN & BRADSTREET DATA MATCH               │
│  - Apple cross-references Legal Entity Name & Address  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            APPLE IDENTITY VERIFICATION CALL            │
│  - Apple representative calls D-U-N-S listed phone     │
│  - Confirms Account Holder legal binding authority     │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Enrollment Approved → Pay $99 Annual Fee → Access Granted ]
```

---

# 4. Annual Renewal & Auto-Renewal Policy

The Apple Developer Program membership MUST be renewed annually ($99 USD/year):

- **Expiration Risk**: If membership expires, active App Store listings are **removed from search**, TestFlight builds stop functioning, and push notifications fail.
- **Auto-Renewal Setup**: Enable Auto-Renewal in App Store Connect under Account -> Membership, backed by a corporate credit card.

---

# 5. Operational Verification Checklist

- [ ] **Corporate Apple Account Used**: Account Holder uses a company-owned domain email address (`apple-developer@company.com`).
- [ ] **2FA Enabled**: Two-Factor Authentication active on Account Holder credentials.
- [ ] **Legal Name Exact Match**: Company name in enrollment matches Dun & Bradstreet record identically.
- [ ] **Auto-Renewal Active**: Annual membership auto-renewal enabled with active corporate card.
- [ ] **Account Holder Backups**: Successor Account Holder transfer procedure documented (see [account-recovery.md](account-recovery.md)).

---

# 6. Related Documentation

- [App Store Connect Handbook](app-store-connect.md) - Portal setup & API keys.
- [Organization Accounts Handbook](organization-accounts.md) - Legal entity setup.
- [Agreements Handbook](agreements.md) - DPLA license agreements.

---

# 7. Official Sources

- Apple Developer Program Enrollment: https://developer.apple.com/programs/enroll/
- Apple D-U-N-S Lookup Tool: https://developer.apple.com/account/#!/dunsLookup

---

**Last verified:** August 14, 2026
