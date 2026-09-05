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

# 4. AI-Assisted Draft Generation

Use this prompt to turn raw release facts into a first-draft plan, then have the Lead Release Engineer and QA Lead review it — the Human Approval Sign-Off Boundary in Section 3 still applies to whatever the AI produces.

```markdown
<system_instructions>
You are a release engineering assistant. Given the release facts below, fill in the Master
Release Plan template: the Release Overview table, a Release Milestone Timeline (diagram +
table), and a Risk Matrix with realistic mitigations and rollback triggers for the stated risk
items. Match the template's existing section structure. Flag any milestone date that looks
unrealistic (e.g. store review scheduled with less than 24-48 hours of buffer).
</system_instructions>

<release_facts>
- App name: <APP_NAME>
- Target version / build numbers: <VERSION_AND_BUILD_NUMBERS>
- Target release date: <DATE>
- Key changes in this release: <LIST>
- Known risk items (new SDKs, API/target changes, payment or auth changes): <LIST>
- Team: Lead Release Engineer <NAME>, QA Lead <NAME>
</release_facts>
```

- [ ] The proposed dates and risk mitigations were actually reviewed, not just copy-pasted in.
- [ ] Section 3's sign-off is completed by a real person before this plan is treated as final.

---

**Last verified:** September 5, 2026

---

# Related documentation

### Templates

- `templates/README.md`
- `templates/app-store-description.md`
- `templates/changelog.md`
- `templates/incident-report.md`
- `templates/play-store-description.md`
- `templates/privacy-questionnaire.md`
- `templates/rejection-response.md`
- `templates/release-checklist.md`
- `templates/release-notes.md`

### AI prompts

- `ai/prompts/release-audit.md`

### Store operations

- `store-operations/README.md`

### Checklists

- `checklists/README.md`

### Post-release

- `post-release/incident-response.md`

### Release strategy

- `release-strategy/release-trains.md`
