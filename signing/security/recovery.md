# Emergency Key Recovery & Compromise Response Plan Handbook

This handbook details compromise containment procedures, revoking compromised certificates, emergency keystore generation, Google Play Upload Key reset requests, and incident post-mortems for **Key Recovery** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to respond when code signing credentials are compromised or lost.

This guide is **not**:

- an authorization mechanism to panic when a signing key is compromised
- a substitute for executing incident containment procedures immediately
- a guide to ignoring key exposure events

---

# 1. Emergency Compromise Containment Flow

```text
┌────────────────────────────────────────────────────────┐
│             KEY COMPROMISE CONTAINMENT FLOW            │
│                                                        │
│  1. INCIDENT DETECTED: Key exposed in git or public log│
│  2. REVOCATION: Revoke compromised certificate in      │
│     Apple Developer Portal immediately.                │
│  3. RESET: Request Upload Key reset in Play Console.  │
│  4. RE-SIGN: Generate new signing identity via Match.  │
│  5. AUDIT: Rotate all affected API keys and secrets.   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Conduct Incident Post-Mortem within 48 Hours ]
```

---

# 2. Key Revocation Protocols

- **iOS Certificate Compromise**: Log into Apple Developer Portal -> Certificates -> Select Compromised Certificate -> Click **Revoke**. Run `fastlane match nuke appstore` to purge old certificates and generate fresh ones.
- **Android Upload Key Compromise**: Request an Upload Key reset in Google Play Console -> Setup -> App Integrity. Upload a new public PEM certificate.

---

# 3. Operational Verification Checklist

- [ ] **Revocation Executed**: Compromised certificate revoked in Apple Developer Portal.
- [ ] **Upload Key Reset Requested**: Upload key reset request submitted to Google Play Console.
- [ ] **API Keys Rotated**: App Store Connect API keys and Service Account JSON keys rotated immediately.
- [ ] **Incident Report File Created**: Outage post-mortem documented in `.releases/incidents/`.

---

# 4. Official Sources

- Incident Report Template: [../../templates/incident-report.md](../../templates/incident-report.md)

---

**Last verified:** August 14, 2026
