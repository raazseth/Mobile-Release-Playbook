# Severe Incident Emergency Release Strategy Handbook

This handbook details severe incident escalation protocols, 2-hour expedited store review procedures, forced update UI modal activation (`minSupportedVersion`), and war room coordination for **Emergency Releases** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to execute emergency releases during P0 security vulnerabilities or catastrophic app crashes.

This guide is **not**:

- an authorization mechanism to bypass executive sign-off for forced version recalls
- a substitute for establishing an Incident Commander and communications channel
- a guide to executing un-coordinated store removals

---

# 1. Emergency Release Escalation Architecture

```text
┌────────────────────────────────────────────────────────┐
│             EMERGENCY RELEASE ESCALATION FLOW          │
│                                                        │
│  1. WAR ROOM ASSEMBLED: Incident Commander assigned.   │
│  2. SEVERITY ASSESSED: P0 Outage / Data Leak confirmed.│
│  3. DUAL-DEPLOYMENT EXECUTED:                          │
│     - Push EAS Update OTA Patch if JS-fixable.         │
│     - Submit Expedited Native Hotfix to App Store.     │
│  4. FORCED UPDATE TRIGGERED: Update backend min version│
│     to block vulnerable client binaries.               │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Forced Upgrade Response Protocol

When an old app version contains a severe security vulnerability, update backend headers to require a minimum version:

```json
{
  "minSupportedVersion": "1.2.1",
  "latestVersion": "1.2.1",
  "forceUpdate": true,
  "message": "A critical security update is required to continue using the application."
}
```

---

# 3. Operational Verification Checklist

- [ ] **War Room Active**: Incident Commander and War Room channel established.
- [ ] **Expedited Review Submitted**: Expedited review request submitted to Apple and Google.
- [ ] **Forced Update Ready**: Backend `minSupportedVersion` updated to block compromised versions.

---

# 4. Official Sources

- Version Recall & Forced Update Handbook: [../post-release/version-recall.md](../post-release/version-recall.md)

---

**Last verified:** August 14, 2026
