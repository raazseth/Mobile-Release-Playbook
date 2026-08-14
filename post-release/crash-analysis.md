# Production Crash Triage & Symbolication Handbook

This handbook details crash triage procedures, crash-free user rate targets (> 99.5%), Hermes stack trace de-obfuscation, `.dSYM` symbolication, and crash grouping for **Crash Analysis** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to triage production crashes efficiently.

This guide is **not**:

- an authorization mechanism to assign un-symbolicated crash logs to engineering
- a substitute for uploading Hermes source maps and `.dSYM` files to Sentry
- a guide to ignoring low-frequency native memory crashes

---

# 1. Production Crash Triage Pipeline

```text
┌────────────────────────────────────────────────────────┐
│             CRASH TRIAGE & SYMBOLICATION PIPELINE      │
│                                                        │
│  - Raw Crash Log Received in Sentry / Crashlytics      │
│  - Match `.dSYM` (iOS) & `mapping.txt` (Android)       │
│  - Match Metro JS Source Maps (Hermes Bytecode)        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Human-Readable Stack Trace Rendered in Defect Tracker ]
```

---

# 2. Uploading Symbol Files via CLI

Automate debug symbol uploads in CI/CD release build workflows:

```bash
# Upload iOS .dSYM symbols to Sentry
sentry-cli debug-files upload --organize ./build/Build/Products/Release-iphoneos/

# Upload JS Source Maps for Hermes De-obfuscation
sentry-cli releases files "com.example.app@1.2.0" upload-sourcemaps ./build/main.jsbundle.map
```

---

# 3. Operational Verification Checklist

- [ ] **`.dSYM` Symbols Uploaded**: Debug symbols uploaded for iOS release build.
- [ ] **Source Maps Uploaded**: JS source maps uploaded for Hermes stack trace symbolication.
- [ ] **Crash-Free Target Met**: Production crash-free user rate maintained > 99.5%.

---

# 4. Official Sources

- Sentry Source Maps Upload: https://docs.sentry.io/platforms/react-native/sourcemaps/

---

**Last verified:** August 14, 2026
