# Post-Release Operations & Monitoring Subsystem Handbook

This directory contains production handbook guides, rollout monitoring protocols, real-time crash triage, emergency hotfix workflows, forced upgrade architectures, and retrospective frameworks for **Post-Release Operations** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to manage application releases post-approval, monitor production telemetry, respond to outages, and execute emergency hotfixes.

This guide is **not**:

- an authorization mechanism to ignore production crash spikes after release approval
- a substitute for monitoring live Android Vitals and Sentry crash reports
- a guide to deploying un-tested hotfixes directly to production

---

# 1. Architecture of Post-Release Operations

Post-release operations govern the lifecycle of a release candidate after store approval, spanning progressive rollout monitoring, real-time crash triage, emergency incident response, hotfix deployment, and post-mortem learning.

```text
┌────────────────────────────────────────────────────────┐
│             POST-RELEASE OPERATIONS LIFECYCLE          │
│                                                        │
│  Stage 1: [rollout-monitoring.md](rollout-monitoring.md)│
│  Stage 2: [monitoring.md](monitoring.md)               │
│  Stage 3: [crash-analysis.md](crash-analysis.md)       │
│  Stage 4: [reviews-and-ratings.md](reviews-and-ratings.md)│
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Normal Operation ]               [ Outage / Crash Spike Detected ]
Continue Rollout Schedule           Trigger Incident Escalation
(1% ──→ 100%)                       -> Halt Rollout ([rollback.md](rollback.md))
                                    -> Emergency Hotfix ([hotfix.md](hotfix.md))
                                    -> Post-Mortem ([release-retrospective.md](release-retrospective.md))
```

---

# 2. Subsystem Directory Taxonomy

| Post-Release Handbook | Operational Scope & Focus Area | Key Metrics & Thresholds |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, post-release architecture, and governance rules. | Lifecycle workflow and operational rules. |
| **[rollout-monitoring.md](rollout-monitoring.md)** | Progressive rollout monitoring (iOS 7-Day & Android Staged). | Phased rollout schedule (1%–100%), crash thresholds. |
| **[monitoring.md](monitoring.md)** | Real-time production telemetry, Sentry, and Datadog APM metrics. | Active users, error rates, adoption tracking. |
| **[crash-analysis.md](crash-analysis.md)** | Crash rate triage, symbolication, and Hermes stack traces. | Crash-free user rate > 99.5%, `.dSYM` upload. |
| **[performance.md](performance.md)** | Live production performance tracking and Android Vitals. | ANR rate < 0.47%, app launch latency < 1.5s. |
| **[analytics.md](analytics.md)** | Product analytics, conversion funnels, and retention tracking. | Feature adoption rate, user retention cohort. |
| **[reviews-and-ratings.md](reviews-and-ratings.md)** | Store ratings, user feedback management, and review prompts. | In-app review prompt (`StoreReview`), rating trends. |
| **[incident-response.md](incident-response.md)** | Emergency incident response, P0 outage escalation, and 5 Whys. | P0 SLA (15 min response), Incident Commander. |
| **[hotfix.md](hotfix.md)** | Expedited emergency hotfix deployment and EAS Update OTA. | Expedited App Review request, Fastlane hotfix branch. |
| **[rollback.md](rollback.md)** | Production rollback strategies and halting staged rollouts. | Pausing Phased Release, halting Staged Rollout, OTA. |
| **[version-recall.md](version-recall.md)** | Critical version removal and forced upgrade enforcement. | `minSupportedVersion` API header, forced update UI. |
| **[release-retrospective.md](release-retrospective.md)** | Blameless post-mortem framework and process improvement. | 5 Whys analysis, corrective action item tracking. |

---

# 3. Universal Post-Release Governance Rules

All post-release operations in this playbook must adhere to five mandatory governance rules:

```text
1. Immediate Rollout Halt on Threshold Breach
   If the crash-free user rate drops below 99.5% or ANR rate exceeds 0.47% during a staged rollout,
   the rollout MUST be halted immediately in App Store Connect or Google Play Console.

2. Mandatory Crash Symbolication
   All production stack traces MUST be symbolicated using `.dSYM` files (iOS) or ProGuard `mapping.txt`
   (Android) before assigning debugging tickets to engineering.

3. Expedited Review Protocol Boundary
   Expedited App Review requests to Apple MUST be reserved strictly for P0 critical production outages,
   data loss bugs, or severe security vulnerabilities.

4. Mandatory 5 Whys Post-Mortem
   Every P0 production outage or emergency hotfix deployment MUST conclude with a written, blameless
   5 Whys retrospective report within 48 hours of resolution.

5. Forced Upgrade Fallback Barrier
   Apps MUST support server-driven `minSupportedVersion` checks to enforce mandatory user upgrades
   when legacy app versions contain un-patchable security vulnerabilities or broken APIs.
```

---

# 4. Related Repository Documentation

- [Incident Report Template](../templates/incident-report.md) - Post-mortem artifact.
- [Production Post-Release Crash Triage](../troubleshooting/crash-after-release.md) - Troubleshooting guide.
- [AI Release Audit Workflow](../ai/workflows/release-audit.md) - AI audit.

---

# 5. Official Sources

- Apple Phased Release Guide: https://developer.apple.com/help/app-store-connect/#/dev3b92cdd7c
- Google Play Staged Rollout Guide: https://support.google.com/googleplay/android-developer/answer/9859348

---

**Last verified:** August 14, 2026
