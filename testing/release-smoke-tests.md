# Release Candidate 10-Minute Smoke Test Suite

This document details the mandatory 10-minute smoke test checklist executed by QA leads and release managers on release candidate builds prior to submitting for store review.

Engineered in alignment with **2026 platform specifications**, it provides an efficient, high-impact verification gate to catch critical release blockers before store submission.

This guide is **not**:

- an authorization mechanism to skip full regression testing
- an optional test suite (every release candidate MUST pass 100% of smoke tests)
- a substitute for automated E2E test execution

---

# 1. 10-Minute Smoke Test Execution Flow

```text
┌────────────────────────────────────────────────────────┐
│             10-MINUTE RELEASE SMOKE TEST FLOW          │
│                                                        │
│  [ Step 1: Fresh Cold Start & Authentication ] (2 min) │
│  [ Step 2: Core Feature Primary Happy Path ]   (3 min) │
│  [ Step 3: StoreKit 2 / Play Billing Paywall ] (2 min) │
│  [ Step 4: Push Notification & Deep Link ]     (2 min) │
│  [ Step 5: Network Offline & Reconnect ]       (1 min) │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ 100% PASS ──→ Submit Release Candidate to Store Review ]
```

---

# 2. Smoke Test Suite Matrix

| Smoke Test # | Test Area | Execution Steps | Expected Pass Criteria |
|---|---|---|---|
| **ST-01** | **Cold Start & Auth** | Launch fresh install; log in with demo account. | App boots < 2s; logs in cleanly; lands on home dashboard. |
| **ST-02** | **Core Primary Flow** | Execute primary app feature (e.g., log workout). | Core flow completes without UI glitch or crash. |
| **ST-03** | **Paywall & IAP** | Tap 'Upgrade to Premium' on settings screen. | Paywall renders price/EULA; StoreKit/Play Sandbox launches. |
| **ST-04** | **Push & Deep Link** | Trigger test push notification; tap banner. | Deep link navigates directly to target screen. |
| **ST-05** | **Offline Transition** | Enable Airplane mode; view screens; re-enable. | Offline banner renders; app auto-syncs when online. |

---

# 3. Operational Verification Checklist

- [ ] **100% Smoke Test Pass**: All 5 smoke tests passed cleanly on physical iOS and Android hardware.
- [ ] **Zero Crash Logs**: Sentry / Crashlytics shows zero errors during 10-minute smoke test session.
- [ ] **Sign-Off Recorded**: Lead QA Engineer signature recorded on release checklist.

---

# 4. Official Sources

- Mobile Release Preparation Workflow: [../../ai/workflows/release-preparation.md](../../ai/workflows/release-preparation.md)

---

**Last verified:** August 14, 2026
