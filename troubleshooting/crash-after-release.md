# Production Post-Release Crash Triage & Symbolication

This document details post-release crash triage, missing `.dSYM` symbolication, Hermes stack trace de-obfuscation, Sentry / Crashlytics triage, and emergency rollout halting for **Production Crashes** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for responding to critical production outages.

This guide is **not**:

- an authorization mechanism to ignore production crash spikes
- a substitute for uploading Hermes source maps
- a guide to delaying emergency rollout halts

---

# 1. Production Crash Incident Response Flow

```text
┌────────────────────────────────────────────────────────┐
│             PRODUCTION CRASH RESPONSE FLOW             │
│                                                        │
│  - Sentry / Crashlytics detects crash spike (> 1%)     │
│  - Identify affected app version & OS distribution     │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Rollout in Progress ]             [ 100% Released ]
Halt Staged Rollout (Play) /        Deploy emergency patch build
Pause Phased Release (App Store)    with incremented build number
```

---

# 2. De-obfuscating Hermes Stack Traces

Production React Native Hermes stack traces show obfuscated bytecode offsets (e.g., `at foo (address at 0x1234)`).

### Resolution
Upload Metro source maps to Sentry CLI to de-obfuscate Hermes stack traces:

```bash
# Upload iOS source map & dSYM symbols to Sentry
sentry-cli releases files "v1.2.0" upload-sourcemaps ./build/main.jsbundle.map
```

---

# 3. Operational Verification Checklist

- [ ] **Rollout Halted**: Phased release paused or staged rollout halted if crash rate exceeds threshold.
- [ ] **Stack Trace Symbolicated**: Debug symbols (`.dSYM` / ProGuard `mapping.txt`) uploaded to Sentry.
- [ ] **Root Cause Fixed**: Code defect resolved and verified locally before compiling patch build.

---

# 4. Official Sources

- Sentry React Native Source Maps: https://docs.sentry.io/platforms/react-native/sourcemaps/

---

**Last verified:** August 14, 2026
