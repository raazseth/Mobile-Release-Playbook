# Playbook Maintenance & Deprecation Policy Handbook

This handbook details quarterly policy audits (90-day verification cycle), deprecation policies for legacy store rules, handling platform API deprecations, and archive maintenance for **Playbook Maintenance**.

Engineered in alignment with **2026 platform specifications**, it details how the playbook stays up-to-date with Apple and Google Play policies.

This guide is **not**:

- an authorization mechanism to retain deprecated platform guidelines as active rules
- a substitute for performing 90-day verification audits
- a guide to silently deleting deprecated documentation without notice

---

# 1. Quarterly Audit & Maintenance Cycle

The Mobile Release Playbook operates on a strict **90-day quarterly verification audit cycle**:

```text
┌────────────────────────────────────────────────────────┐
│             QUARTERLY AUDIT CYCLE (90-DAY)             │
│                                                        │
│  - Q1 Audit (Jan): Audit WWDC & Play Policy Roadmap   │
│  - Q2 Audit (Apr): Audit Spring Store Policy Updates   │
│  - Q3 Audit (Jul): Audit Target SDK & Xcode Releases   │
│  - Q4 Audit (Oct): Audit Fall Platform SDK Launches    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Re-Verify Platform Specs & Update `Last verified` Timestamps ]
```

---

# 2. Store Policy Deprecation Protocol

When Apple or Google Play deprecate an API or submission rule (e.g., Apple deprecating legacy Bitcode or FCM deprecating legacy HTTP APIs):

1. **Mark as Deprecated**: Update the corresponding handbook to mark the feature as `DEPRECATED` with an explicit deprecation date notice.
2. **Provide Replacement**: Document the mandatory replacement API (e.g., FCM HTTP v1 API).
3. **Archive Legacy Content**: Move retired documentation to `.archived/` directory after 12 months.

---

# 3. Operational Verification Checklist

- [ ] **90-Day Audit Cycle Active**: All handbook files audited within the past 90 days.
- [ ] **Deprecations Documented**: Deprecated APIs marked explicitly with replacement instructions.
- [ ] **`Last verified` Current**: All modified files updated to current verification date.

---

# 4. Official Sources

- Apple News & Policy Updates: https://developer.apple.com/news/
- Google Play Policy Updates: https://support.google.com/googleplay/android-developer/answer/9924652

---

**Last verified:** August 14, 2026
