# CI/CD Pipeline & EAS Build Failures Triage

This document details diagnostic workflows, missing EAS secrets, Xcode runner version mismatches, GitHub Actions runner timeouts, and environment variable failures for **CI/CD Pipeline Failures** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to troubleshoot automated build and submission runner failures.

This guide is **not**:

- an authorization mechanism to embed secrets in CI configuration files
- a substitute for verifying local builds before triggering CI runners
- a guide to ignoring runner environment variable scope

---

# 1. CI Pipeline Failure Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             CI/CD RUNNER FAILURE DIAGNOSTIC            │
│                                                        │
│  - Inspect GitHub Actions / EAS Build runner logs      │
│  - Check environment secrets & API credentials        │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Missing Secrets / Key ]           [ Runner Timeout / Memory ]
Create secret via `eas secret`      Increase runner RAM or cache
or GitHub Repository Secrets        node_modules in CI pipeline
```

---

# 2. Key CI Error Scenarios & Resolutions

### Scenario A: EAS Build `EXPO_TOKEN Not Found`
- **Symptom**: `Error: Authentication token is missing`.
- **Resolution**: Generate EAS Access Token in Expo Dashboard -> Add to GitHub Secrets as `EXPO_TOKEN`.

### Scenario B: Xcode Runner macOS SDK Mismatch
- **Symptom**: `error: The requested SDK "iphoneos18.0" is not installed`.
- **Resolution**: Update GitHub Actions runner image to `macos-14` or `macos-15` supporting Xcode 16+.

---

# 3. Operational Verification Checklist

- [ ] **EAS Token Active**: `EXPO_TOKEN` verified set in GitHub Secrets.
- [ ] **Xcode Runner Version Matched**: CI runner image supports targeted iOS SDK.
- [ ] **Caching Configured**: `node_modules` and CocoaPods cached in CI runner to prevent timeouts.

---

# 4. Official Sources

- Expo EAS Build Troubleshooting: https://docs.expo.dev/build/troubleshooting/
- GitHub Actions Documentation: https://docs.github.com/en/actions

---

**Last verified:** August 14, 2026
