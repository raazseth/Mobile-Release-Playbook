# End-to-End (E2E) Testing Automation & Maestro/Detox Workflows

This document details automated end-to-end UI testing frameworks, Maestro YAML flows, Detox configuration, iOS Simulator & Android Emulator automation, and CI runner integration for **E2E Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to automate full user journey testing across complete application builds.

This guide is **not**:

- an authorization mechanism to skip testing critical user journeys
- a substitute for running smoke tests on physical devices
- a guide to writing flaky E2E tests relying on hardcoded thread sleeps

---

# 1. E2E Framework Comparison (Maestro vs Detox)

| Feature / Metric | Maestro Framework | Detox Framework |
|---|---|---|
| **Setup Complexity** | **Low** (Declarative YAML flows) | **Medium/High** (Native iOS/Android configs) |
| **Execution Speed** | **Fast** (Black-box UI driver) | **Very Fast** (Gray-box in-process synchronization) |
| **Platform Support** | iOS Simulators & Android Emulators | iOS Simulators & Android Emulators |
| **Flakiness Resistance**| High (Built-in auto-retry & element polling) | High (Synchronization with React Native thread) |

---

# 2. Maestro Declarative E2E Flow Example (`login-flow.yaml`)

Maestro uses clean declarative YAML files to define end-to-end user flows:

```yaml
# .maestro/login-flow.yaml
appId: com.example.mobileapp
---
- launchApp:
    clearState: true

# Assert Splash Screen Transitions to Login
- assertVisible: "Welcome Back"

# Input Login Credentials
- tapOn: "Email Address Input"
- inputText: "test_qa_user@example.com"
- tapOn: "Password Input"
- inputText: "SecurePass2026!"

# Tap Submit Button
- tapOn: "Log In"

# Assert Successful Navigation to Home Dashboard
- assertVisible: "Home Dashboard"
- assertVisible: "Recommended Workouts"
```

### Running Maestro CLI

```bash
# Run Maestro E2E test flow on connected simulator/emulator
maestro test .maestro/login-flow.yaml
```

---

# 3. GitHub Actions CI Matrix E2E Test Pipeline

```yaml
name: E2E Automation Pipeline

on:
  pull_request:
    branches: [main]

jobs:
  e2e-android:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Java & Android SDK
        uses: actions/setup-java@v4
        with:
          distribution: 'zulu'
          java-version: '17'

      - name: Run Maestro E2E Test Suite
        uses: mobile-dev-inc/action-maestro@v1
        with:
          flow: .maestro/login-flow.yaml
```

---

# 4. Operational Verification Checklist

- [ ] **Core Flows Covered**: E2E tests cover Login, Main Feature, and Checkout flows.
- [ ] **No Thread Sleep**: Flaky `sleep(5000)` calls replaced with explicit element assertions.
- [ ] **CI Automation Active**: E2E test matrix executes automatically on pull request creation.
- [ ] **Failure Artifacts Captured**: Screenshots and video recordings saved on E2E test failure.

---

# 5. Official Sources

- Maestro E2E Framework Docs: https://maestro.mobile.dev/
- Detox E2E Framework Docs: https://wix.github.io/Detox/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Testing

- `testing/README.md`
- `testing/accessibility.md`
- `testing/android.md`
- `testing/deep-links.md`
- `testing/device-matrix.md`
- `testing/device-testing.md`
- `testing/integration.md`
- `testing/ios.md`
- `testing/localization.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/payments.md`
- `testing/performance.md`
- `testing/push-notifications.md`
- `testing/release-smoke-tests.md`
- `testing/unit.md`
- `testing/upgrade-migrations.md`

### Pre-release

- `pre-release/README.md`
- `pre-release/final-release-checklist.md`

### Checklists

- `checklists/pre-release.md`

### Troubleshooting

- `troubleshooting/README.md`

### Release strategy

- `release-strategy/beta-testing.md`
