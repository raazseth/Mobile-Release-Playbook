# Pre-Release Quality Audit Automation Subsystem Handbook

This directory contains production handbook guides, CLI scripts, and automated quality gate audit tools for **Pre-Release Audit** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to execute pre-flight quality gate audits automatically.

This guide is **not**:

- an authorization mechanism to bypass pre-release security and privacy audits
- a substitute for running `npm audit` vulnerability scans
- a guide to submitting builds targeting outdated Android API levels (< API Level 36)

---

# 1. Pre-Release Audit Architecture

The pre-release audit toolchain runs a series of quality gate checks covering security, dependency vulnerabilities, privacy manifests, and configuration parameters.

```text
┌────────────────────────────────────────────────────────┐
│             PRE-RELEASE QUALITY AUDIT PIPELINE         │
│                                                        │
│  [ Pre-Release Audit CLI (`audit-release.js`) ]        │
│                         │                              │
│                         ▼                              │
│  - Dependency Audit (`npm audit --audit-level=high`)   │
│  - Target SDK 36 Check (`targetSdkVersion = 36`)      │
│  - Privacy Manifest (`PrivacyInfo.xcprivacy` check)    │
│  - Non-Exempt Encryption (`ITSAppUsesNonExempt...`)    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ PASS (Exit 0) ──→ Build Cleared for Release Candidate ]
```

---

# 2. Key Automation Handbooks

- **[audit-release.md](audit-release.md)**: Automated pre-flight quality gate audit script guide & implementation.

---

# 3. Operational Verification Checklist

- [ ] **Dependency Scan Passed**: Zero High or Critical vulnerabilities reported by `npm audit`.
- [ ] **Target SDK 36 Verified**: Android `targetSdkVersion` set to 36 (Android 16).
- [ ] **Privacy Manifest Verified**: `PrivacyInfo.xcprivacy` present and populated with Required Reason APIs.

---

# 4. Official Sources

- OWASP Mobile Top 10 Security: https://owasp.org/www-project-mobile-top-10/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Release audit scripts

- `scripts/release-audit/audit-release.md`

### Pre-release

- `pre-release/release-readiness.md`

### AI agents

- `ai/agents/release-auditor.md`
