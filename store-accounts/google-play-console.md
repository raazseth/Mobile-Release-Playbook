# Google Play Console Developer Account Administration

This document details the registration requirements, Organization D-U-N-S® verification, Personal account closed testing requirements (12 testers for 14 days), Google Search Console domain verification, and administration protocols for **Google Play Console Developer Accounts**.

Engineered in alignment with **2026 platform specifications**, it specifies how to establish and maintain an Organization Developer Account ($25 USD one-time registration fee) for Android application publishing, internal/production tracks, Play Billing 8.0/9.0+, and Real-Time Developer Notifications (RTDN).

This guide is **not**:

- an authorization mechanism to share root Google account passwords
- a guide to bypassing the 12-tester closed testing rule for personal accounts
- a substitute for verifying Google Cloud Payments profiles

---

# 1. Google Play Console Account Types & Requirements

Google Play Console defines two distinct developer account categories with different verification standards and publishing requirements.

```text
                               GOOGLE PLAY CONSOLE ACCOUNTS
                                             │
      ┌──────────────────────────────────────┴──────────────────────────────────────┐
      ▼                                                                             ▼
[ ORGANIZATION ACCOUNT ]                                                   [ PERSONAL ACCOUNT ]
- Requires D-U-N-S® Number                                                - For individual developers
- Verified Business Documents & Payments Profile                           - Subject to MANDATORY Closed Testing:
- Direct Production Access upon approval                                   - 12 Opted-in Testers for 14 Days
- Recommended for all commercial apps                                     - Before production track unlock
```

---

# 2. Organization Account Requirements & D-U-N-S® Verification

All Organization developer accounts created in Google Play Console MUST complete business identity verification:

1. **D-U-N-S® Number**: Mandatory 9-digit D-U-N-S Number from Dun & Bradstreet. Business name and address MUST match the Google Payments Profile identically.
2. **Official Business Documents**: Upload official articles of incorporation, business license, or tax registration certificates.
3. **Google Search Console Website Verification**: Verify ownership of your company's official website via Google Search Console.
4. **Registration Fee**: One-time $25 USD registration fee paid via corporate credit card.

---

# 3. Personal Account Closed Testing Gate (12 Testers / 14 Days)

Personal developer accounts created after November 2023 are subject to mandatory testing gates before production access is granted:

```text
[ Personal Developer Account Created ]
                  │
                  ▼
[ Upload Android App Bundle (.aab) to Closed Testing Track ]
                  │
                  ▼
┌────────────────────────────────────────────────────────┐
│             MANDATORY CLOSED TESTING GATE              │
│  - Recruit 12 Opted-In Testers                         │
│  - Testers MUST remain opted-in for 14 CONTINUOUS DAYS │
└──────────────────────────┬─────────────────────────────┘
                  │
                  ▼ (14 Days Complete)
[ Apply for Production Track Access → Subject to Manual Google Review ]
```

> **COMMERCIAL RECOMMENDATION**: Commercial applications MUST use an **Organization Account** to bypass the personal testing gate and establish enterprise governance.

---

# 4. API & Service Account Key Isolation

Automated deployment tools (Fastlane Supply, EAS Submit) connect to Google Play Console via **Google Cloud Service Accounts**:

```text
Google Cloud Console ──→ [ Create Service Account ] ──→ [ Grant Service Account Access in Play Console ]
                                                                   │
                                                                   ▼
                                                   [ Export JSON Key to Secret Vault ]
```

- **Permission Scoping**: Grant service accounts only the specific permissions needed (e.g., *Release to testing tracks*, *Manage store listings*).
- **Secret Isolation**: Store Service Account JSON keys in managed CI secrets; never commit keys to git repositories.

---

# 5. Operational Verification Checklist

- [ ] **Organization Account Verified**: D-U-N-S Number and business registration documents approved.
- [ ] **Google Search Console Synced**: Official website domain verified in Search Console.
- [ ] **Service Account Isolated**: Deployment service account JSON key stored in CI secret vault.
- [ ] **Developer Contact Info Up-to-Date**: Public developer email and address verified in Play Console settings.
- [ ] **2FA Enforced**: All Google accounts with Play Console access enforce 2-Step Verification.

---

# 6. Related Documentation

- [Apple Developer Handbook](apple-developer.md) - Apple Developer accounts.
- [Organization Accounts Handbook](organization-accounts.md) - Legal entity comparison.
- [Roles & Permissions Handbook](roles-and-permissions.md) - Role RBAC matrices.

---

# 7. Official Sources

- Google Play Console Account Verification: https://support.google.com/googleplay/android-developer/answer/10788668
- Google Play Closed Testing Requirements: https://support.google.com/googleplay/android-developer/answer/9845334

---

**Last verified:** August 14, 2026

