# Account Recovery & Disaster Preparedness

This document details the emergency account recovery procedures, Account Holder transfer mechanics, two-factor authentication (2FA) device loss protocols, domain ownership recovery, and business continuity plans for **Apple Developer Program & Google Play Console Accounts**.

Engineered in alignment with **2026 platform specifications**, it specifies how to maintain continuous administrative control over developer accounts during executive departures, lost hardware keys, or account compromise.

This guide is **not**:

- an authorization mechanism to bypass store identity security checks
- a guide to using un-verified personal recovery accounts
- a substitute for maintaining off-site corporate credential backups

---

# 1. Threat Scenarios & Business Continuity

Losing administrative access to an Apple Developer or Google Play Console Account halts all mobile software delivery, disables customer support access, and prevents hotfix deployment.

```text
┌────────────────────────────────────────────────────────┐
│               ACCOUNT DISASTER THREAT MATRIX           │
│                                                        │
│  - Executive / Account Holder Departure                │
│  - Lost 2FA Hardware Device / Security Key             │
│  - Corporate Email / Domain Expiration                 │
│  - Suspected Account Compromise / Unauthorized Access  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            EMERGENCY RECOVERY PROTOCOL PIPELINE        │
│  - Activate Corporate Recovery Keys                    │
│  - Execute Account Holder Transfer (Apple Support)     │
│  - Re-verify Corporate Domain Ownership                │
└────────────────────────────────────────────────────────┘
```

---

# 2. Account Holder Transfer Protocol (Apple Developer)

When the designated Account Holder leaves the organization or transitions roles, ownership MUST be transferred cleanly to a successor:

```text
Current Account Holder ──→ [ App Store Connect → Users and Access ] ──→ [ Transfer Account Holder Role ]
                                                                                │
                                                                                ▼
                                                                [ Successor Accepts Transfer ]
```

### Requirements for Account Holder Transfer

1. Both current and successor Account Holders MUST have active 2FA enabled on their Apple Accounts.
2. The successor MUST be an existing **Admin** user on the App Store Connect account.
3. The successor MUST use a corporate domain email address (`apple-developer@company.com`).

---

# 3. 2FA Device Loss & Emergency Recovery Keys

To prevent lockout caused by a lost phone or hardware security key:

- **Corporate Recovery Keys**: Generate and securely store Apple Account and Google Account Emergency Recovery Keys in a corporate password vault (1Password Teams / Bitwarden Enterprise) accessible by at least two authorized corporate officers.
- **Backup Admin Account**: Ensure at least **two** independent employees hold full Admin access to App Store Connect and Google Play Console.

---

# 4. Emergency Incident Response: Account Compromise

If suspicious or unauthorized activity is detected on a developer account (e.g., unrecognized build uploads or unauthorized team additions):

1. **Revoke Session Tokens**: Log out all active sessions and change Account Holder / Admin passwords immediately.
2. **Revoke API Keys**: Instantly delete compromised `.p8` App Store Connect API keys and Google Play Service Account JSON keys.
3. **Notify Platform Security**: Submit emergency compromise reports to Apple Security / Google Play Developer Support.
4. **Audit Audit Logs**: Inspect App Store Connect and Play Console activity logs to identify unauthorized modifications.

---

# 5. Operational Verification Checklist

- [ ] **Dual Admin Access**: At least two authorized corporate officers hold Admin access.
- [ ] **2FA Recovery Keys Backed Up**: Emergency 2FA recovery keys stored securely in corporate vault.
- [ ] **Corporate Email Assigned**: Account Holder uses a role-based corporate email (`apple-developer@company.com`).
- [ ] **Successor Succession Plan**: Account Holder transfer procedure documented for executive transitions.
- [ ] **API Key Revocation Ready**: Procedure established to revoke `.p8` keys instantly during security incidents.

---

# 6. Related Documentation

- [Apple Developer Handbook](apple-developer.md) - Account Holder setup.
- [Google Play Console Handbook](google-play-console.md) - Play Console setup.
- [Roles & Permissions Handbook](roles-and-permissions.md) - Role RBAC matrices.

---

# 7. Official Sources

- Apple Account Holder Transfer Guide: https://developer.apple.com/support/account-holder-transfer/
- Google Account Recovery for Businesses: https://support.google.com/a/answer/33561

---

**Last verified:** August 14, 2026
