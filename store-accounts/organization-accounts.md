# Organization vs Individual Store Accounts

This document details the architectural comparison, legal entity requirements, D-U-N-S® business identity matching, legal binding authority verification, and multi-team governance for **Organization vs Individual Store Accounts** in Apple App Store Connect and Google Play Console.

Engineered in alignment with **2026 platform specifications**, it specifies why commercial applications must be published under Organization Developer Accounts and how to navigate identity verification without enrollment rejection.

This guide is **not**:

- an authorization mechanism to register fake or fictitious business entities
- a guide to transferring personal accounts without legal entity verification
- a substitute for Dun & Bradstreet identity record verification

---

# 1. Individual vs Organization Account Comparison

Choosing between an Individual and Organization developer account determines your app's public store branding, legal liability exposure, team management capabilities, and verification requirements.

```text
┌────────────────────────────────────────────────────────┐
│             INDIVIDUAL DEVELOPER ACCOUNT               │
│  - Public Seller Name: Personal Developer Full Name    │
│  - Single-User Access (No team role management on iOS) │
│  - Subject to Google Play 12-Tester / 14-Day Gate     │
└────────────────────────────────────────────────────────┘
                           vs
┌────────────────────────────────────────────────────────┐
│             ORGANIZATION DEVELOPER ACCOUNT             │
│  - Public Seller Name: Official Legal Business Name    │
│  - Multi-User RBAC Team Permissions & API Keys         │
│  - Requires D-U-N-S® Number & Business Registration    │
│  - Bypasses Personal Account Closed Testing Gates      │
└────────────────────────────────────────────────────────┘
```

| Dimension | Individual Account | Organization Account |
|---|---|---|
| **Public Store Seller Name** | Personal First/Last Name (e.g., "John Doe") | Legal Business Name (e.g., "Acme Mobile Technologies LLC") |
| **Verification Requirement** | Personal Government ID / Identity Check | D-U-N-S® Number + Official Business Registration Docs |
| **Team Role Permissions (RBAC)** | Limited (Single-user primary account on Apple) | Full Multi-User RBAC (Admin, App Manager, Developer, Finance) |
| **Google Play Closed Testing Gate** | **MANDATORY**: 12 Testers for 14 Days | **EXEMPT**: Direct production access upon verification |
| **Legal Liability Exposure** | Personal liability | Corporate entity protection |

---

# 2. Pre-Enrollment D-U-N-S® Data Matching Protocol

D-U-N-S® verification failures are the leading cause of enrollment delays. Store review systems perform automated regex string matching between enrollment submissions and Dun & Bradstreet records.

```text
┌────────────────────────────────────────────────────────┐
│             D-U-N-S® EXACT STRING MATCHING             │
│                                                        │
│  - Legal Entity Name MUST match D-U-N-S record 100%    │
│  - Street Address, Suite #, City, Zip MUST match       │
│  - Corporate Phone Number MUST be reachable for call   │
└────────────────────────────────────────────────────────┘
```

### Common Rejection Mismatches

- **DBA / Trade Names**: Enrolling using a "Doing Business As" (DBA) name instead of the registered legal entity name (e.g., enrolling as "Acme Apps" instead of "Acme Technologies Corporation").
- **Address Discrepancies**: Suite numbers or street abbreviations mismatching D-U-N-S records (e.g., "Suite 400" vs "Ste 400").
- **Unreachable Phone Number**: Phone number in D-U-N-S database is disconnected, preventing Apple/Google identity verification calls.

---

# 3. Legal Binding Authority Verification

Both Apple and Google require that the person enrolling an organization possesses explicit legal authority to bind the company to financial and legal agreements:

- **Authorized Roles**: Company Owner, Founder, Executive Officer (CEO, CTO, CFO), or an employee with explicit legal power of attorney granted by an executive.
- **Verification Call**: Apple conducts a phone call to the corporate phone number listed in the D-U-N-S registry to confirm the Account Holder's title and authority before approving the account.

---

# 4. Operational Verification Checklist

- [ ] **Organization Account Selected**: Commercial applications enrolled under an Organization account.
- [ ] **D-U-N-S Match Verified**: Legal business name and address match D-U-N-S record string-for-string.
- [ ] **Corporate Email Used**: Account Holder uses a domain email address (`apple-developer@company.com`).
- [ ] **Phone Verification Ready**: Corporate phone number in D-U-N-S registry is active to receive verification call.
- [ ] **Corporate Bank Account Linked**: Bank account for payouts belongs to the legal entity name, not a personal individual.

---

# 5. Related Documentation

- [Apple Developer Handbook](apple-developer.md) - Apple Developer setup.
- [Google Play Console Handbook](google-play-console.md) - Google Play setup.
- [Agreements Handbook](agreements.md) - Legal agreements.

---

# 6. Official Sources

- Apple Organization Enrollment Guidance: https://developer.apple.com/support/enrollment/
- Dun & Bradstreet D-U-N-S Overview: https://www.dnb.com/duns-number.html

---

**Last verified:** August 14, 2026
