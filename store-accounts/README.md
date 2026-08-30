# Store Accounts Governance & Administration

This directory contains production handbook guides, security posture rules, role permission matrices, tax/banking workflows, legal agreement management, and disaster recovery procedures for **Apple Developer Program & Google Play Console Developer Accounts**.

Engineered in alignment with **2026 platform requirements**, it specifies how to enroll, verify, govern, and maintain organization developer accounts while avoiding account suspensions, legal agreement blocks, or payout holds.

This directory is **not**:

- a shortcut for bypassing D-U-N-S® business identity verification
- an authorization mechanism to share administrative passwords or 2FA credentials
- a substitute for legal counsel when accepting Developer Program Agreements

---

# 1. Architecture of Developer Store Accounts

Developer accounts form the administrative umbrella for all mobile application binaries, store listings, push notification certificates, API keys, in-app purchases, and financial payouts.

```text
┌────────────────────────────────────────────────────────┐
│               LEGAL ENTITY & ORGANIZATION              │
│    (D-U-N-S® Verified Corp/LLC + Apple Account w/ 2FA) │
└──────────────────────────┬─────────────────────────────┘
                           │
             [ Admin & Role Governance ]
                           │
    ┌──────────────────────┴──────────────────────┐
    ▼                                             ▼
┌────────────────────────┐               ┌────────────────────────┐
│ Apple Developer        │               │ Google Play Console    │
│ Program ($99/yr)       │               │ Developer Account ($25)│
└───────────┬────────────┘               └───────────┬────────────┘
            │                                        │
            ├──────────────────────┬─────────────────┤
            ▼                      ▼                 ▼
┌────────────────────────┐┌────────────────┐┌────────────────────┐
│ App Store Connect      ││ Agreements &   ││ Banking, Tax &     │
│ (Builds, Meta, Keys)   ││ Legal Sign-Off ││ Financial Payouts  │
└────────────────────────┘└────────────────┘└────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Purpose & Scope | Key Platform & Store Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, store account architecture, and universal account rules. | High-level account governance, 2026 identity rules. |
| **[apple-developer.md](apple-developer.md)** | Apple Developer Program enrollment, Account Holder setup, D-U-N-S verification, and 2FA. | $99/yr annual renewal, Legal Entity validation, Account Holder authority. |
| **[google-play-console.md](google-play-console.md)** | Google Play Console setup, Organization D-U-N-S rules, and Personal account testing gates. | $25 fee, mandatory D-U-N-S for Organizations, 12-tester / 14-day closed testing. |
| **[app-store-connect.md](app-store-connect.md)** | App Store Connect portal management, team invites, API Keys (`.p8`), and TestFlight. | App Store Connect API keys, User Access roles, Sandbox tester setup. |
| **[organization-accounts.md](organization-accounts.md)** | Individual vs Organization account comparison and binding authority verification. | D-U-N-S lookup, legal name matching, phone verification calls. |
| **[roles-and-permissions.md](roles-and-permissions.md)** | Role permission matrices across Apple App Store Connect and Google Play Console. | Account Holder, Admin, App Manager, Developer, Finance, Support permissions. |
| **[banking-and-tax.md](banking-and-tax.md)** | Paid Applications Agreements, W-8BEN-E / W-9 forms, backup withholding, and banking. | US Tax forms, 24% backup withholding defense, SWIFT/IBAN routing. |
| **[agreements.md](agreements.md)** | Developer License Agreement updates (DPLA / DDA), addendums, and compliance blocks. | Acceptance deadlines, Account Holder exclusive sign-off, build release blocks. |
| **[account-recovery.md](account-recovery.md)** | Account Holder transfer, 2FA device recovery, business continuity, and emergency response. | Corporate recovery keys, domain ownership proof, successor delegation. |

---

# 3. Universal Account Governance Rules

All store account administration in this playbook must adhere to five mandatory engineering rules:

```text
1. Organization Accounts Required for Commercial Apps
   Commercial applications MUST be published under an Organization Developer Account (requires
   D-U-N-S® Number). Individual developer accounts expose personal names and lack multi-user RBAC.

2. Mandatory Two-Factor Authentication (2FA) & Corporate Apple Accounts
   All Account Holder and Admin accounts MUST enforce 2FA using hardware security keys or corporate-owned
   devices. Never enroll store accounts using personal employee Apple Accounts or Gmail addresses.

3. Exclusive Account Holder Agreement Sign-Off
   Only the designated Account Holder (Owner) has legal authority to accept updated Developer Program
   License Agreements (DPLA). Delaying agreement acceptance halts app updates globally across stores.

4. Secret Isolation for API Keys
   App Store Connect API Keys (.p8) and Google Play Service Account JSON keys MUST be stored in managed
   CI/CD secret vaults. Raw private keys MUST NEVER be shared over Slack/email or committed to git.

5. Pre-Enrollment D-U-N-S® Data Verification
   The legal business name, street address, and contact phone number MUST match identically across Dun &
   Bradstreet, government business registries, and store payment profiles to prevent verification rejection.
```

---

# 4. Related Repository Documentation

- [Signing & Credentials](../signing/README.md) - Certificates and provisioning profiles.
- [Mobile Release Lifecycle](../foundations/mobile-release-lifecycle.md) - Release lifecycle.
- [Monetization Subsystem](../monetization/README.md) - In-app purchases and subscriptions.

---

# 5. Official Sources

- Apple Developer Program Enrollment: https://developer.apple.com/programs/enroll/
- Google Play Console Identity Verification: https://support.google.com/googleplay/android-developer/answer/10788668
- Dun & Bradstreet D-U-N-S Lookup: https://developer.apple.com/account/#!/dunsLookup

---

**Last verified:** August 14, 2026

