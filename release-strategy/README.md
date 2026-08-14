# Release Strategy & Progressive Deployment Governance Handbook

This directory contains production handbook guides, fixed-cadence release train schedules, iOS 7-Day Phased Release workflows, Android Google Play Staged Rollouts, TestFlight/Play Beta testing protocols, remote feature flagging architectures, dynamic kill switches, and emergency hotfix deployment rules for **Release Strategy** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to mitigate blast radius and control feature distribution to millions of users.

This guide is **not**:

- an authorization mechanism to release 100% production deployments instantaneously to all users without staged rollouts
- a substitute for decoupling code deployment from feature enablement via remote feature flags
- a guide to shipping un-tested hotfixes without QA audit sign-off

---

# 1. Architecture of Mobile Progressive Release Strategy

Mobile release strategy controls how updates transition from development branches through progressive rollout cohorts to 100% global user adoption.

```text
┌────────────────────────────────────────────────────────┐
│             PROGRESSIVE RELEASE ARCHITECTURE           │
│                                                        │
│  Stage 1: Beta & Pre-Release Testing                  │
│           (TestFlight 10,000 Testers + Play Closed Testing)│
│                         │                              │
│                         ▼                              │
│  Stage 2: Fixed-Cadence Release Train Cutoff           │
│           (Bi-Weekly Branch Cut `release/v1.2.0`)      │
│                         │                              │
│                         ▼                              │
│  Stage 3: Progressive Store Rollout                    │
│           - iOS Phased Release (Day 1: 1% → Day 7: 100%)│
│           - Android Staged Rollout (1% → 10% → 100%)   │
│                         │                              │
│                         ▼                              │
│  Stage 4: Remote Feature Flagging & Kill Switches      │
│           (Decoupled Feature Toggles via Remote Config)│
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Strategy Handbook | Primary Focus & Scope | Key Rules & Thresholds |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, release strategy architecture, and governance rules. | Master taxonomy and rollout rules. |
| **[release-trains.md](release-trains.md)** | Fixed-cadence bi-weekly release train governance and cutoff schedules. | 14-day train cycle, feature uncoupling. |
| **[phased-release.md](phased-release.md)** | iOS 7-Day Phased Release schedule in App Store Connect. | Day 1: 1% → Day 7: 100%, halt criteria. |
| **[staged-rollouts.md](staged-rollouts.md)** | Google Play Staged Rollout strategy and cohort escalation. | 1% → 10% → 50% → 100%, ANR/Crash gates. |
| **[beta-testing.md](beta-testing.md)** | TestFlight External (10k testers) and Play Closed Testing (12/14 days). | Public links, beta feedback SLAs. |
| **[feature-flags.md](feature-flags.md)** | Remote feature flag architecture (LaunchDarkly, Firebase, PostHog). | Dynamic targeting, percentage rollouts. |
| **[kill-switches.md](kill-switches.md)** | Dynamic remote kill switches and emergency fallback UI components. | Circuit breakers, local config cache. |
| **[hotfixes.md](hotfixes.md)** | Emergency hotfix branch deployment (`hotfix/v1.2.1`) and EAS OTA patches. | Fast-track QA, expedited review. |
| **[emergency-release.md](emergency-release.md)** | Severe incident emergency releases (P0 security/crash) & forced updates. | 2-hour expedited review, forced upgrade UI. |

---

# 3. Universal Release Strategy Governance Rules

All release strategies in this playbook MUST comply with five mandatory governance rules:

```text
1. Mandatory Staged Rollout Default
   ALL production releases MUST utilize staged rollouts (iOS 7-Day Phased Release and Android Staged Rollout).
   Immediate 100% blast-radius releases are FORBIDDEN except for verified emergency P0 hotfixes.

2. Automated Rollout Halting Thresholds
   If a progressive rollout triggers a crash-free rate drop below 99.5% or an Android Vitals ANR rate
   exceeding 0.47%, the rollout MUST BE HALTED IMMEDIATELY.

3. Decouple Code Deployment from Feature Release
   High-risk new features MUST be wrapped in remote feature flags, enabling code to ship safely
   inactive and be enabled progressively post-release.

4. Dynamic Remote Kill Switch Mandate
   Every major integration (new payment gateway, third-party SDK, AI service) MUST include a dynamic
   remote kill switch to disable the feature remotely without requiring an emergency store update.

5. Strict 14-Day Release Train Cadence
   Release trains depart on a strict 14-day schedule. Incomplete features MUST NOT delay the train;
   they MUST be hidden behind feature flags or left on feature branches for the next train.
```

---

# 4. Related Repository Documentation

- [Post-Release Operations Handbook](../post-release/README.md) - Monitoring & Incident Response.
- [Pre-Release Readiness Handbooks](../pre-release/README.md) - GO/NO-GO quality gates.
- [Store Operations Handbooks](../store-operations/README.md) - Review submission.

---

# 5. Official Sources

- Apple Phased Release Guidance: https://developer.apple.com/help/app-store-connect/release-an-app-update/phased-release
- Google Play Staged Rollouts: https://support.google.com/googleplay/android-developer/answer/6346149

---

**Last verified:** August 14, 2026
