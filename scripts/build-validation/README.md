# Build Validation Automation Subsystem Handbook

This directory contains production handbook guides, CLI scripts, and automated inspection tools for **Build Validation** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to inspect compiled binary artifacts (`.ipa` and `.aab`) automatically before store upload.

This guide is **not**:

- an authorization mechanism to upload un-inspected binaries to App Store Connect or Play Console
- a substitute for verifying R8 ProGuard class stripping
- a guide to ignoring missing Hermes bytecode compilation

---

# 1. Build Validation Inspection Architecture

Build validation unpacks compiled `.ipa` and `.aab` artifacts to verify native binary architecture, asset dimensions, alpha channel transparency, and byte sizes.

```text
┌────────────────────────────────────────────────────────┐
│             BUILD VALIDATION INSPECTION FLOW           │
│                                                        │
│  [ Compiled Binary Artifact (.ipa / .aab) ]            │
│                         │                              │
│                         ▼                              │
│  - Inspect Hermes Bytecode (`index.android.bundle`)    │
│  - Inspect App Icon (1024x1024 no alpha transparency)  │
│  - Inspect 6.9" Display Screenshots (1320x2868 px)     │
│  - Inspect Target SDK (Android 16 API 36)             │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Automated Validation Report ──→ Exit 0 (Pass) / Exit 1 (Fail) ]
```

---

# 2. Key Automation Handbooks

- **[validate-build.md](validate-build.md)**: Automated build & asset validation script guide & implementation.

---

# 3. Operational Verification Checklist

- [ ] **Artifact Inspected**: Compiled binary file exists and size is > 5MB.
- [ ] **Alpha Transparency Stripped**: App icon verified free of 32-bit RGBA alpha channel.
- [ ] **Hermes Verified**: Hermes bytecode header magic bytes validated in bundle.

---

# 4. Official Sources

- Android App Bundle Format: https://developer.android.com/guide/app-bundle

---

**Last verified:** August 14, 2026

---

# Related documentation

### Build validation scripts

- `scripts/build-validation/validate-build.md`

### Pre-release

- `pre-release/configuration-audit.md`

### Foundations

- `foundations/project-configuration.md`
