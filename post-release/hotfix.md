# Emergency Hotfix Release Protocol & Expedited Review

This handbook details emergency hotfix branch management (`hotfix/v1.2.1`), Expedited App Review request procedures, EAS Update Over-The-Air (OTA) patch deployment, and hotfix testing for **Hotfix Releases** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to deploy emergency hotfixes rapidly and safely.

This guide is **not**:

- an authorization mechanism to deploy un-tested code during hotfix emergencies
- a substitute for requesting Apple Expedited App Review when native hotfixes are required
- a guide to using EAS Update OTA patches for native binary changes (OTA is for JS bundle changes only)

---

# 1. Hotfix Deployment Paths (OTA vs Native Binary)

Depending on whether the production bug resides in JavaScript code or native native code (`iOS`/`Android`), hotfixes follow one of two deployment paths:

```text
                                EMERGENCY HOTFIX PATHS
                                          │
       ┌──────────────────────────────────┴──────────────────────────────────┐
       ▼                                                                     ▼
[ EAS UPDATE OTA PATCH ]                                    [ EXPEDITED NATIVE BINARY HOTFIX ]
- JS / Asset bug fix only                                  - Native code, C++, or manifest fix
- Deployed in < 5 minutes via EAS CLI                      - Git `hotfix/v1.2.1` branch compiled
- Bypasses store review                                    - Requires Apple Expedited App Review
```

---

# 2. Deploying JS Hotfix via EAS Update (`eas update`)

```bash
# Publish Over-The-Air (OTA) JS hotfix to production channel
eas update --channel production --message "Hotfix: Fix checkout crash on null price"
```

---

# 3. Requesting Apple Expedited App Review

When a hotfix requires a new native binary (`.ipa`), submit an Expedited App Review request in App Store Connect under Contact Us -> Request an Expedited App Review. Select "Critical Bug Fix" and detail the P0 outage impact.

---

# 4. Operational Verification Checklist

- [ ] **Hotfix Branch Created**: `hotfix/v1.2.1` branched from release tag `v1.2.0`.
- [ ] **Scope Minimization**: Hotfix contains ONLY the minimal code edit required to fix the P0 defect.
- [ ] **EAS OTA / Native Path Selected**: JS-only bug deployed via EAS Update; native bug submitted with Expedited Review request.

---

# 5. Official Sources

- Apple Expedited Review Guidance: https://developer.apple.com/support/expedited-review/
- Expo EAS Update Guide: https://docs.expo.dev/eas-update/introduction/

---

**Last verified:** August 14, 2026
