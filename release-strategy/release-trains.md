# Fixed-Cadence Release Train Governance Handbook

This handbook details fixed-cadence release train schedules (14-day train cycle), release candidate cutoffs, un-coupling feature readiness from ship dates, train master roles, and branch cut automation for **Release Trains** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to maintain predictable release cadences across large engineering teams.

This guide is **not**:

- an authorization mechanism to delay release train departures for incomplete features
- a substitute for using remote feature flags to guard incomplete code
- a guide to shipping un-audited code on release train branches

---

# 1. Release Train Cadence & Lifecycle Schedule

The Mobile Release Train operates on a strict **14-day (bi-weekly) cadence**:

```text
┌────────────────────────────────────────────────────────┐
│             14-DAY RELEASE TRAIN LIFECYCLE             │
│                                                        │
│  Day 1 (Mon): Branch Cut (`release/v1.2.0` from `main`)│
│  Day 1–3:     Release Candidate Smoke Testing & Audits │
│  Day 4 (Thu): Submit to App Store Review & Play Closed │
│  Day 5 (Fri): Store Approval Received                  │
│  Day 8 (Mon): Start iOS Phased Release & Play Rollout │
│  Day 14 (Sun): 100% Global Adoption Achieved           │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Release Train Governance Rules

1. **The Train Departs on Time**: If a feature is not 100% complete and tested by Day 1 Branch Cutoff, it MUST NOT board the train. The feature stays on its feature branch for the next train 14 days later.
2. **Train Master Role**: A designated Release Engineer acts as Train Master for each 14-day cycle, overseeing branch cuts, QA audits, store submissions, and progressive rollout monitoring.

---

# 3. Operational Verification Checklist

- [ ] **Branch Cut Automated**: CI script cuts `release/v1.X.0` branch every 2nd Monday at 09:00 UTC.
- [ ] **Train Master Assigned**: Train Master assigned and logged in release calendar.
- [ ] **Incomplete Code Flagged**: Incomplete work guarded behind feature flags or excluded from branch.

---

# 4. Official Sources

- Mobile Release Train Best Practices: https://martinfowler.com/articles/ship-events.html

---

**Last verified:** August 14, 2026

---

# Related documentation

### Release strategy

- `release-strategy/README.md`
- `release-strategy/beta-testing.md`
- `release-strategy/emergency-release.md`
- `release-strategy/feature-flags.md`
- `release-strategy/hotfixes.md`
- `release-strategy/kill-switches.md`
- `release-strategy/phased-release.md`
- `release-strategy/staged-rollouts.md`

### Post-release

- `post-release/rollout-monitoring.md`
- `post-release/rollback.md`

### Release engineering

- `release-engineering/release-pipelines.md`

### Expo

- `frameworks/expo/updates.md`

### Foundations

- `foundations/release-environments.md`
