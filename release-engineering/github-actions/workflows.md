# GitHub Actions Production Workflow Specifications

This handbook details production workflow YAML specifications (`.github/workflows/release-pipeline.yml`), matrix build jobs, tag triggers, environment secrets, and caching strategies for **GitHub Actions Workflows** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides production-ready GitHub Actions YAML workflows.

This guide is **not**:

- an authorization mechanism to run builds without caching dependency layers
- a substitute for locking runner image versions
- a guide to hardcoding passwords in workflow YAML files

---

# 1. Production Release Workflow Specification (`release-pipeline.yml`)

```yaml
name: Production Release Pipeline

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  audit-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Run Unit & Integration Tests
        run: npm test

  build-ios:
    needs: audit-and-test
    runs-on: macos-14
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install Expo EAS CLI
        run: npm install -g eas-cli

      - name: Build & Submit iOS
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
        run: eas build --platform ios --profile production --non-interactive --auto-submit

  build-android:
    needs: audit-and-test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install Expo EAS CLI
        run: npm install -g eas-cli

      - name: Build & Submit Android
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
        run: eas build --platform android --profile production --non-interactive --auto-submit
```

---

# 2. Operational Verification Checklist

- [ ] **Tag Trigger Verified**: Workflow triggers on `v*.*.*` tag push.
- [ ] **Parallel Matrix Jobs**: `build-ios` and `build-android` run concurrently after `audit-and-test`.
- [ ] **Non-Interactive Mode Set**: `--non-interactive` flag passed to CLI commands in GitHub Actions.

---

# 3. Official Sources

- GitHub Actions Workflow Syntax: https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions

---

**Last verified:** August 14, 2026

---

# Related documentation

### GitHub Actions

- `release-engineering/github-actions/README.md`
- `release-engineering/github-actions/action-templates.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/environment-management.md`

### Signing security

- `signing/security/ci-secrets.md`
