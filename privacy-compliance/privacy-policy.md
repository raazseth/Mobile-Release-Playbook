# Privacy Policy Requirements & Public Hosting Handbook

This handbook details the mandatory clauses, public URL hosting requirements (`https://` HTTP 200 OK check), user data rights disclosures, and store console integration for **Privacy Policies** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive guidance for drafting and maintaining compliant mobile privacy policies.

This guide is **not**:

- an authorization mechanism to host privacy policies behind password login walls
- a substitute for consulting legal counsel
- a static policy document (privacy policies must update when app data collection changes)

---

# 1. Privacy Policy Public Hosting Requirements

Store consoles mandate an active, publicly accessible Privacy Policy link before accepting app submissions:

```text
┌────────────────────────────────────────────────────────┐
│             PRIVACY POLICY HOSTING RULES               │
│                                                        │
│  - Active `https://` Web URL (HTTP 200 OK)             │
│  - No Password or Authentication Gate                  │
│  - Accessible directly via Web Browser                 │
│  - Plain text or readable HTML format                  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Configured in App Store Connect & Google Play Console App Info ]
```

---

# 2. Mandatory Privacy Policy Clauses

A compliant mobile privacy policy MUST explicitly address six core data dimensions:

1. **What Data Is Collected**: List all personal data types (Name, Email, Location, Device IDs, Purchase History).
2. **How Data Is Collected**: Disclose collection mechanisms (Direct user input, automated SDK telemetry, cookies).
3. **Why Data Is Collected**: Disclose purposes (App functionality, account management, analytics, advertising).
4. **Third-Party Data Sharing**: Disclose all third-party SDK partners receiving data (e.g., Sentry, Firebase, RevenueCat, AppsFlyer).
5. **User Data Rights**: Disclose rights to inspect, export, or request account/data deletion.
6. **Contact Information**: Provide a valid legal contact email address (e.g., `privacy@example.com`).

---

# 3. Operational Verification Checklist

- [ ] **`https://` Link Live**: Privacy Policy URL tested and returns HTTP 200 OK in browser.
- [ ] **No Login Wall**: Web page accessible without login credentials or paywall.
- [ ] **Third-Party SDKs Disclosed**: Sentry, Firebase, Analytics, and MMP partners listed.
- [ ] **Data Deletion Process Explained**: Clear instructions provided for requesting data deletion.

---

# 4. Official Sources

- Apple Privacy Policy Requirements: https://developer.apple.com/app-store/review/guidelines/#privacy-policy
- Google Play Privacy Policy Requirements: https://support.google.com/googleplay/android-developer/answer/9859455

---

**Last verified:** August 14, 2026
