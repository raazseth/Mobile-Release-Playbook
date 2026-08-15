# Pre-Release Audits & Quality Gates Subsystem

This directory contains production handbook guides, release readiness frameworks, security audits, dependency vulnerability scans, privacy manifest verifications, and pre-flight quality gates for **Pre-Release Engineering** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to conduct systematic audits across code, assets, metadata, security, and compliance before authorizing a production store release.

This guide is **not**:

- an authorization mechanism to bypass release readiness gates
- a substitute for executing automated CI/CD build pipelines
- a informal verbal-only sign-off process

---

# 1. Architecture of Pre-Release Audit Quality Gates

Pre-release engineering enforces a sequence of 10 specialized audit gates to evaluate a release candidate binary before granting GO/NO-GO release authorization.

```text
┌────────────────────────────────────────────────────────┐
│             PRE-RELEASE AUDIT QUALITY GATES            │
│                                                        │
│  Gate 1: [dependency-audit.md](dependency-audit.md)    │
│  Gate 2: [security-audit.md](security-audit.md)        │
│  Gate 3: [privacy-audit.md](privacy-audit.md)          │
│  Gate 4: [permissions-audit.md](permissions-audit.md)  │
│  Gate 5: [configuration-audit.md](configuration-audit.md)│
│  Gate 6: [asset-audit.md](asset-audit.md)              │
│  Gate 7: [metadata-audit.md](metadata-audit.md)        │
│  Gate 8: [performance-audit.md](performance-audit.md)  │
│  Gate 9: [accessibility-audit.md](accessibility-audit.md)│
│  Gate 10: [final-release-checklist.md](final-release-checklist.md)│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             RELEASE READINESS DECISION (GO / NO-GO)   │
│  - [release-readiness.md](release-readiness.md)        │
│  - Requires unanimous sign-off from Lead Engineer,     │
│    QA Lead, Security Lead, and Product Manager          │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Audit Handbook | Primary Audit Domain & Scope | Key Verification Checkpoints |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, quality gate architecture, and governance rules. | High-level audit pipeline and governance rules. |
| **[release-readiness.md](release-readiness.md)** | Master GO/NO-GO release readiness decision framework. | GO/NO-GO criteria, P0–P3 severity, sign-off protocol. |
| **[final-release-checklist.md](final-release-checklist.md)** | Final release verification checklist prior to store upload. | Binary packaging, store metadata, signing, QA sign-off. |
| **[dependency-audit.md](dependency-audit.md)** | Dependency security, vulnerability scans, and license audits. | `npm audit`, `bun audit`, Podfile/Gradle lockfiles. |
| **[security-audit.md](security-audit.md)** | App security, OWASP Mobile Top 10, ATS, and token vaults. | OWASP Mobile Top 10, ATS, ProGuard R8, SecureStore. |
| **[privacy-audit.md](privacy-audit.md)** | Privacy manifests, Required Reason APIs, and Data Safety. | `PrivacyInfo.xcprivacy`, Xcode Privacy Report, Data Safety. |
| **[permissions-audit.md](permissions-audit.md)** | Runtime permissions, Plist usage strings, and Android 14+ photos. | Usage description strings, Android 14+ Selected Photos. |
| **[configuration-audit.md](configuration-audit.md)** | Build configuration, `targetSdkVersion 36`, and `eas.json`. | Target SDK 36, `ITSAppUsesNonExemptEncryption`, bundle IDs. |
| **[asset-audit.md](asset-audit.md)** | Visual media assets, app icons, screenshots, and feature graphics. | 1024x1024 App Icon no alpha, 6.9" screenshots (1320x2868). |
| **[metadata-audit.md](metadata-audit.md)** | Store metadata, Guideline 2.3 accuracy, and competitor brand ban. | App Title 30c, Subtitle 30c, Keywords 100c, Guideline 2.3.7. |
| **[performance-audit.md](performance-audit.md)** | App performance, Hermes JS heap, 60 FPS scrolling, and TTI. | 60/120 FPS scrolling, TTI < 1.5s, Android Vitals ANR < 0.47%. |
| **[accessibility-audit.md](accessibility-audit.md)** | Mobile accessibility, screen readers, and WCAG contrast ratios. | VoiceOver/TalkBack, `accessibilityLabel`, WCAG 4.5:1. |

---

# 3. Universal Pre-Release Governance Rules

All pre-release audit implementations in this playbook must adhere to five mandatory governance rules:

```text
1. Zero P0/P1 Open Defect Mandate
   A release candidate binary MUST NOT receive a `GO` decision if any P0 (Critical Blocker) or
   P1 (High Severity) vulnerability or bug remains open.

2. Automated Audit Script Execution
   All static audits (dependency vulnerability scans, privacy manifest checks, configuration checks)
   MUST be automated in CI runners (`npm run audit:release`) prior to human review.

3. Unanimous Stakeholder Sign-Off
   A `GO` release readiness decision requires explicit sign-offs from the Lead Engineer, QA Lead,
   Security Lead, and Product Lead. Any single stakeholder can issue a `NO-GO` veto.

4. Release Candidate Binary Immutability
   Once a release candidate build passes all pre-release audits, the exact compiled binary (.ipa / .aab)
   MUST be promoted to production. Re-compiling source code invalidates all prior audit passes.

5. Audit Log Archival
   All audit reports, test execution logs, and sign-off records MUST be archived in repository root
   under `.releases/v1.2.0/audit-report.md` for compliance auditing.
```

---

# 4. Related Repository Documentation

- [Release Readiness Workflow](../ai/workflows/release-audit.md) - AI release audit.
- [Release Preparation Workflow](../ai/workflows/release-preparation.md) - Release preparation.
- [Master Release Checklist](../templates/release-checklist.md) - Checklist template.

---

# 5. Official Sources

- Apple App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/
- Google Play Developer Policies: https://play.google.com/about/developer-content-policy/

---

**Last verified:** August 14, 2026
