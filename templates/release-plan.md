# Master Release Plan Template

Use this operational template to plan, schedule, risk-assess, and track production mobile application releases across iOS and Android platforms.

---

# Release Overview

| Release Parameter | Release Target Value |
|---|---|
| **App Name** | `[App Name]` |
| **Target Version (Marketing)** | `v[1.2.0]` |
| **iOS Build Number (`buildNumber`)** | `[1.2.0.1]` |
| **Android Build Number (`versionCode`)** | `[143]` |
| **Target Release Date** | `[YYYY-MM-DD]` |
| **Lead Release Engineer** | `[Engineer Name / Contact]` |
| **QA Lead Owner** | `[QA Lead Name / Contact]` |

---

# 1. Release Milestone Schedule

```text
┌────────────────────────────────────────────────────────┐
│             RELEASE MILESTONE TIMELINE                 │
│                                                        │
│  [Code Freeze] ──→ [Internal QA] ──→ [Beta Testing]    │
│       │                   │                 │          │
│  [YYYY-MM-DD]        [YYYY-MM-DD]      [YYYY-MM-DD]    │
│                                                        │
│  [Store Review] ──→ [Staged Rollout] ──→ [100% Release]│
│       │                   │                 │          │
│  [YYYY-MM-DD]        [YYYY-MM-DD]      [YYYY-MM-DD]    │
└────────────────────────────────────────────────────────┘
```

| Milestone Phase | Target Date | Status | Owner |
|---|---|---|---|
| **1. Code Freeze** | `[YYYY-MM-DD]` | `[PENDING / COMPLETED]` | Lead Engineer |
| **2. Internal QA & E2E Testing** | `[YYYY-MM-DD]` | `[PENDING / COMPLETED]` | QA Lead |
| **3. TestFlight & Beta Release** | `[YYYY-MM-DD]` | `[PENDING / COMPLETED]` | QA Lead |
| **4. Store Submission** | `[YYYY-MM-DD]` | `[PENDING / COMPLETED]` | Release Manager |
| **5. Staged Rollout (1% -> 50%)** | `[YYYY-MM-DD]` | `[PENDING / COMPLETED]` | Release Manager |
| **6. Full Release (100%)** | `[YYYY-MM-DD]` | `[PENDING / COMPLETED]` | Release Manager |

---

# 2. Risk Matrix & Rollback Triggers

### Risk Assessment

| Risk Item | Impact | Severity | Mitigation Strategy | Owner |
|---|---|---|---|---|
| **New Payment Gateway SDK** | High | P1 | Test on Sandbox endpoints; feature flag rollback ready. | Backend Lead |
| **Target API 36 Upgrade** | Medium | P2 | Validate permission prompts on Android 16 emulator. | Mobile Lead |
| **Third-Party SDK Update** | Medium | P2 | Inspect `PrivacyInfo.xcprivacy` for Required Reason APIs. | Mobile Lead |

### Emergency Rollback & Halt Triggers

```text
CRITICAL ROLLBACK TRIGGERS:
- Crash-free user metric drops below 99.0% on Sentry / Crashlytics.
- User-perceived ANR rate exceeds 0.47% on Google Play Console.
- Payment checkout conversion drops by > 15% post-release.
- Critical authentication or security vulnerability discovered.

EMERGENCY ACTION PROTOCOL:
1. Immediately Halt Android Staged Rollout in Play Console.
2. Pause iOS Phased Release in App Store Connect.
3. Enable Emergency Maintenance Feature Flag on backend servers.
4. Notify Lead Release Engineer & Executive Stakeholders.
```

---

# 3. Human Approval Sign-Off Boundary

| Stakeholder Role | Name & Title | Approval Status | Signature / Date |
|---|---|---|---|
| **Lead Release Engineer** | `[Name]` | `[APPROVED / PENDING]` | `[Signature / Date]` |
| **QA Lead Owner** | `[Name]` | `[APPROVED / PENDING]` | `[Signature / Date]` |
| **Product Manager** | `[Name]` | `[APPROVED / PENDING]` | `[Signature / Date]` |

---

**Last verified:** August 14, 2026
