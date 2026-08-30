# Beta Testing & Community Feedback Strategy Handbook

This handbook details TestFlight Internal (100 testers) vs External (10,000 testers), Google Play Closed Testing (mandatory 12-tester / 14-day gate for personal developer accounts), public beta links, and feedback SLAs for **Beta Testing** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to structure pre-release beta testing programs.

This guide is **not**:

- an authorization mechanism to skip Google Play Personal account closed testing requirements
- a substitute for collecting crash reports and feedback from TestFlight beta users
- a guide to distributing un-signed beta builds

---

# 1. Beta Testing Track Architecture

Beta testing isolates pre-release candidates across distinct internal employee and external public community testing groups.

```text
┌────────────────────────────────────────────────────────┐
│             BETA TESTING TRACK ARCHITECTURE            │
│                                                        │
│  - Track 1: Internal Employees (TestFlight / Play Internal)│
│    (Immediate deployment, 100 testers max, zero review) │
│                                                        │
│  - Track 2: External Beta Testers (TestFlight / Play Closed)│
│    (10,000 testers max, requires Beta App Review)      │
│    (Google Personal Accounts: Mandatory 12 testers / 14 days)│
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Platform Beta Rules (2026)

1. **Google Play Personal Account Rule**: Personal developer accounts created after Nov 2023 MUST run a closed test with at least **12 testers for 14 continuous days** before applying for production access.
2. **Apple Beta App Review**: TestFlight builds distributed to External Groups require initial Beta App Review by Apple.

---

# 3. Operational Verification Checklist

- [ ] **Internal Group Active**: Internal testing group created with core engineering team members.
- [ ] **External Group Configured**: TestFlight Public Link generated and shared with beta community.
- [ ] **14-Day Gate Completed**: Google Play closed testing gate verified complete for personal accounts.

---

# 4. Official Sources

- TestFlight Overview: https://developer.apple.com/testflight/
- Google Play Closed Testing Requirements: https://support.google.com/googleplay/android-developer/answer/9845334

---

**Last verified:** August 14, 2026

---

# Related documentation

### Release strategy

- `release-strategy/README.md`
- `release-strategy/emergency-release.md`
- `release-strategy/feature-flags.md`
- `release-strategy/hotfixes.md`
- `release-strategy/kill-switches.md`
- `release-strategy/phased-release.md`
- `release-strategy/release-trains.md`
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
