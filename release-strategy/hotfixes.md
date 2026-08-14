# Emergency Hotfix Deployment Protocol Handbook

This handbook details emergency hotfix branch management (`hotfix/v1.2.1`), patch scope validation, fast-track testing, Apple Expedited Review requests, and Expo EAS Update OTA hotfix patching for **Hotfixes** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to deploy emergency hotfixes safely under high-pressure outage scenarios.

This guide is **not**:

- an authorization mechanism to bundle new feature code into an emergency hotfix
- a substitute for obtaining QA sign-off prior to submitting an expedited hotfix
- a guide to skipping regression testing on critical core flows

---

# 1. Dual-Path Hotfix Deployment Architecture

Emergency hotfixes follow one of two deployment paths depending on whether the bug involves JS-only logic or native code:

```text
┌────────────────────────────────────────────────────────┐
│             DUAL-PATH HOTFIX ARCHITECTURE              │
│                                                        │
│  - Path A: JS-Only Bug Fix ──→ EAS Update OTA Patch    │
│    (Deployed to production CDN in < 15 minutes)        │
│                                                        │
│  - Path B: Native Code Bug Fix ──→ Expedited Store Review│
│    (Branch `hotfix/v1.2.1` ──→ Expedited App Review request)│
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Expedited Store Review Request Protocol

For native hotfixes requiring store submission:
1. Cut `hotfix/v1.2.1` from tag `v1.2.0`.
2. Apply minimal code fix strictly targeting P0 bug.
3. Submit build via Fastlane / EAS Submit.
4. Fill out **Expedited App Review Request Form** in App Store Connect, citing severe production crash details.

---

# 3. Operational Verification Checklist

- [ ] **Minimal Scope Enforced**: Hotfix commit contains ONLY the P0 bug fix (zero feature additions).
- [ ] **EAS OTA Evaluated**: Evaluated whether fix can be deployed via JS-only OTA update.
- [ ] **Fast-Track Testing Passed**: Hotfix passes 10-minute Release Candidate Smoke Test suite.

---

# 4. Official Sources

- Apple Expedited App Review Requests: https://developer.apple.com/contact/app-store/?topic=expedite
- Emergency Hotfix Handbook: [../post-release/hotfix.md](../post-release/hotfix.md)

---

**Last verified:** August 14, 2026
