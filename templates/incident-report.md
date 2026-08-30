# Production Release Incident & Outage Post-Mortem Template

Use this operational template to document, triage, analyze root causes, and define prevention action items following a production release incident or critical crash spike.

---

# Incident Overview

| Incident Parameter | Value |
|---|---|
| **Incident Identifier** | `INC-[2026-0814-01]` |
| **App Name & Version** | `[App Name] v[1.2.0]` (Build `[1.2.0.1]`) |
| **Incident Severity** | `[P0 - Critical / P1 - High]` |
| **Incident Status** | `[RESOLVED / MONITORING]` |
| **Incident Commander** | `[Name / Contact]` |
| **Incident Date & Time** | `[YYYY-MM-DD HH:MM UTC]` |
| **Resolution Date & Time** | `[YYYY-MM-DD HH:MM UTC]` |

---

# 1. Incident Executive Summary

Provide a concise 2–3 sentence summary of what failed, the user impact, and how the incident was resolved:

> *During the v1.2.0 production rollout on 2026-08-14, a regression in the payment processing module caused checkout screen crashes for users on iOS 18. The incident affected approximately 2.4% of active users over a 3-hour window. The release was halted in App Store Connect, and hotfix build v1.2.1 was compiled and released to resolve the issue.*

---

# 2. Incident Timeline

```text
┌────────────────────────────────────────────────────────┐
│             INCIDENT RESPONSE TIMELINE                 │
│                                                        │
│  [14:00 UTC] ──→ Release v1.2.0 Deployed (Staged 10%)  │
│  [14:25 UTC] ──→ Sentry Spike Alert Fired (Crash > 3%) │
│  [14:35 UTC] ──→ Incident Commander Halts Rollout     │
│  [15:10 UTC] ──→ Root Cause Identified (Null Pointer) │
│  [16:30 UTC] ──→ Hotfix Build v1.2.1 Uploaded & Passed │
│  [17:00 UTC] ──→ Release Resumed & Metrics Normal     │
└────────────────────────────────────────────────────────┘
```

---

# 3. Root Cause Analysis (5 Whys)

1. **Why did the app crash?** The payment checkout button triggered an un-handled NullPointer Exception on iOS 18 devices.
2. **Why was the object null?** The native bridge initialization failed to pass the updated API token key.
3. **Why failed?** A breaking SDK change was updated without adjusting the bridge parameter signature.
4. **Why was it not caught in QA?** E2E testing ran against mock payment endpoints that bypassed native bridge initialization.
5. **Why were mock tests bypass-configured?** Test suite lacked physical device sandbox execution for payment flows.

---

# 4. Corrective & Preventative Action Items

| Action Item | Action Type | Priority | Owner | Target Due Date |
|---|---|---|---|---|
| Add physical device sandbox E2E test for payments | Prevention | P1 | QA Lead | `[YYYY-MM-DD]` |
| Add strict TypeScript non-null assertion checks | Code Quality | P2 | Mobile Lead | `[YYYY-MM-DD]` |
| Update Sentry alert threshold for instant Slack alerts | Monitoring | P1 | DevOps Lead | `[YYYY-MM-DD]` |

---

# Sign-Off Verification

- **Incident Commander Approval**: `[ Signature / Date ]`
- **Lead Release Engineer Approval**: `[ Signature / Date ]`

---

**Last verified:** August 14, 2026

---

# Related documentation

### Templates

- `templates/README.md`
- `templates/app-store-description.md`
- `templates/changelog.md`
- `templates/play-store-description.md`
- `templates/privacy-questionnaire.md`
- `templates/rejection-response.md`
- `templates/release-checklist.md`
- `templates/release-notes.md`
- `templates/release-plan.md`

### Store operations

- `store-operations/README.md`

### Checklists

- `checklists/README.md`

### Post-release

- `post-release/incident-response.md`

### Release strategy

- `release-strategy/release-trains.md`
