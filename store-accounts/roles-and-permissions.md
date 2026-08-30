# Roles & Permissions RBAC Matrices

This document details the Role-Based Access Control (RBAC) security architecture, user permission matrices, least-privilege principles, and API key scoping rules across **Apple App Store Connect** and **Google Play Console**.

Engineered in alignment with **2026 platform specifications**, it specifies how to assign granular user roles to developers, QA engineers, release managers, finance teams, and automated CI/CD service accounts while protecting sensitive financial data and legal agreement authorities.

This guide is **not**:

- an authorization mechanism to grant blanket "Admin" roles to all team members
- a justification for sharing single-user login credentials across multiple employees
- a bypass for mandatory 2FA enforcement on administrative accounts

---

# 1. Least-Privilege Role Security Architecture

Granting excessive administrative privileges to team members or automated CI/CD keys exposes organizations to credential hijacking, unauthorized release submissions, and accidental app deletions.

```text
┌────────────────────────────────────────────────────────┐
│               LEAST-PRIVILEGE SECURITY RULE            │
│                                                        │
│   Users and automated service keys MUST be granted the │
│   MINIMUM permissions necessary to perform their role. │
│   Only the Account Holder / Owner possesses legal      │
│   agreement sign-off authority.                        │
└────────────────────────────────────────────────────────┘
```

---

# 2. Apple App Store Connect RBAC Permission Matrix

App Store Connect defines seven primary user roles:

| App Store Connect Role | Manage Users? | Edit Metadata / Upload Builds? | Create API Keys? | View Sales & Finance? | Legal Agreement Sign-Off? |
|---|---|---|---|---|---|
| **Account Holder (Owner)** | Yes | Yes | Yes | Yes | **YES (Exclusive)** |
| **Admin** | Yes | Yes | Yes | Yes | No |
| **App Manager** | No | Yes (Assigned Apps) | Yes (Assigned Apps) | No | No |
| **Developer** | No | Upload Builds Only | Read-Only | No | No |
| **Customer Support** | No | Respond to Reviews Only | No | No | No |
| **Marketing** | No | Edit Metadata & Screenshots | No | No | No |
| **Finance** | No | No | No | **YES** | No |

---

# 3. Google Play Console RBAC Permission Matrix

Google Play Console provides granular permission checkboxes that can be grouped into standard operational roles:

| Google Play Role | Release to Production? | Release to Testing Tracks? | Edit Store Listings? | View Financial Reports? | Admin (Manage Users)? |
|---|---|---|---|---|---|
| **Account Owner** | Yes | Yes | Yes | Yes | **YES** |
| **Release Manager** | Yes | Yes | Yes | No | No |
| **Developer / CI Key** | No | **YES** | No | No | No |
| **Store Marketer** | No | No | **YES** | No | No |
| **Financial Analyst** | No | No | No | **YES** | No |
| **Customer Support** | No | No | Reply to Reviews | No | No |

---

# 4. CI/CD Service Account & API Key Permission Scoping

Automated toolchains (Fastlane, EAS Submit, GitHub Actions) MUST operate under scoped credentials:

- **Build Upload Tools (EAS / Fastlane)**: Assign **App Manager** role in App Store Connect; grant *Release to testing tracks* permission in Google Play Console.
- **Metadata Management Tools**: Assign **App Manager** or **Marketing** role.
- **Status Checking Tools**: Assign **Developer** or read-only roles.

---

# 5. Operational Verification Checklist

- [ ] **No Blanket Admins**: Admin role is restricted to designated Lead Release Engineers.
- [ ] **Single Account Holder**: Only one active Account Holder / Owner exists per store account.
- [ ] **CI Keys Scoped**: Automated CI/CD service keys use App Manager or test track roles.
- [ ] **Finance Role Restricted**: Access to sales reports and banking setup is restricted to accounting staff.
- [ ] **Quarterly Role Audit**: User access lists audited quarterly to revoke access for departed contractors or employees.

---

# 6. Related Documentation

- [App Store Connect Handbook](app-store-connect.md) - API key management.
- [Google Play Console Handbook](google-play-console.md) - Service account keys.
- [Agent Permissions](../ai/security/agent-permissions.md) - AI agent sandboxing.

---

# 7. Official Sources

- Apple App Store Connect User Roles: https://developer.apple.com/help/app-store-connect/#/dev8b49e0c52
- Google Play Console User Permissions: https://support.google.com/googleplay/android-developer/answer/9844686

---

**Last verified:** August 14, 2026

