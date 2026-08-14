# Live Production Telemetry & Real-Time Monitoring Handbook

This handbook details production monitoring setups, real-time error tracking via Sentry, Datadog / Firebase APM metrics, active user adoption tracking, and alert webhook integrations for **Production Monitoring** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to monitor live application health post-release.

This guide is **not**:

- an authorization mechanism to ignore error rate alerts
- a substitute for configuring version-specific telemetry filters
- a guide to storing PII in telemetry logs

---

# 1. Real-Time Telemetry Pipeline Architecture

```text
┌────────────────────────────────────────────────────────┐
│             REAL-TIME TELEMETRY PIPELINE               │
│                                                        │
│  [ Production Mobile App (v1.2.0) ]                     │
│               │                                        │
│       ┌───────┴───────┐                                │
│       ▼               ▼                                │
│  [ Sentry Errors ] [ Datadog APM / Firebase ]          │
│       │               │                                │
│       └───────┬───────┘                                │
│               ▼                                        │
│  [ Slack / PagerDuty Alert Webhooks ]                  │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Telemetry Metrics & Alert Triggers

1. **Crash-Free Sessions %**: Alert if crash-free sessions drop below **99.5%**.
2. **HTTP 5xx Server Error Rate**: Alert if API network failure rate exceeds **1%**.
3. **Unhandled JS Exceptions**: Alert on new JS exception signatures introduced in release build.

---

# 3. Operational Verification Checklist

- [ ] **Sentry Environment Configured**: Sentry SDK configured with environment (`production`) and release (`v1.2.0`).
- [ ] **Alert Webhooks Active**: Slack / PagerDuty alerts configured for critical crash spikes.
- [ ] **Adoption Dashboard Live**: Dashboard tracks user migration from legacy versions to release build.

---

# 4. Official Sources

- Sentry React Native Docs: https://docs.sentry.io/platforms/react-native/

---

**Last verified:** August 14, 2026
