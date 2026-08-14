# Dependency Security & Vulnerability Audit Handbook

This handbook details vulnerability scanning (`npm audit`, `bun audit`), native dependency lockfile audits (`Podfile.lock`, `build.gradle`), license compliance verification, and supply chain security for **Dependency Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit third-party dependencies before approving a release candidate.

This guide is **not**:

- an authorization mechanism to release apps containing known high-severity CVE vulnerabilities
- a substitute for auditing native iOS CocoaPods and Android Gradle dependencies
- a guide to using un-vetted third-party packages

---

# 1. Dependency Audit Architecture

Software supply chain security requires auditing JavaScript/TypeScript packages (`package.json`), iOS CocoaPods (`Podfile.lock`), and Android Gradle dependencies.

```text
┌────────────────────────────────────────────────────────┐
│             DEPENDENCY AUDIT LAYERS                    │
│                                                        │
│  - JS/TS Layer: `npm audit --audit-level=high`        │
│  - iOS Native Layer: `pod lib lint` & CocoaPods Specs │
│  - Android Native Layer: Gradle Dependency Verification│
│  - License Compliance: Audit OSI-approved licenses     │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Free of High/Critical Vulnerabilities & Copyleft Licenses ]
```

---

# 2. CLI Automated Dependency Audit Commands

Run dependency vulnerability scans in local development and CI runners:

```bash
# Audit npm dependencies for High and Critical vulnerabilities
npm audit --audit-level=high

# Verify zero un-approved copyleft licenses (GPL/AGPL)
npx license-checker --onlyAllow "MIT;BSD-3-Clause;Apache-2.0;ISC"
```

---

# 3. Operational Verification Checklist

- [ ] **Zero High/Critical CVEs**: `npm audit` returns zero High or Critical vulnerability findings.
- [ ] **Lockfiles Committed**: `package-lock.json` / `bun.lockb` and `Podfile.lock` committed to git.
- [ ] **Licenses Compliant**: All third-party dependencies use OSI-approved permissive licenses (MIT, Apache 2.0, BSD).
- [ ] **Native Pods Audited**: All CocoaPods dependencies verified compatible with iOS 18 SDK.

---

# 4. Official Sources

- npm Audit Documentation: https://docs.npmjs.com/cli/v10/commands/npm-audit
- OWASP Dependency-Check: https://owasp.org/www-project-dependency-check/

---

**Last verified:** August 14, 2026
