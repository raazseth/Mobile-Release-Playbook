# GitHub Actions CI/CD Subsystem Handbook

This directory contains production handbook guides, GitHub Actions workflow YAML specifications (`release-pipeline.yml`), matrix build jobs, dependency caching (`node_modules`, Gradle, CocoaPods), and action templates for **GitHub Actions** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to automate mobile CI/CD pipelines using GitHub Actions.

This guide is **not**:

- an authorization mechanism to run un-cached build jobs on GitHub Actions runners
- a substitute for using `macos-14` / `macos-15` runner images for Xcode 16+ builds
- a guide to hardcoding secret keys in `.github/workflows/` YAML files

---

# 1. GitHub Actions Matrix Build Pipeline Architecture

GitHub Actions runs parallel iOS and Android matrix jobs triggered by git release tag pushes.

```text
┌────────────────────────────────────────────────────────┐
│             GITHUB ACTIONS WORKFLOW MATRIX             │
│                                                        │
│  [ Git Push Tag `v1.2.0` ]                             │
│             │                                          │
│             ▼                                          │
│  [ Job 1: Test & Audit (ubuntu-latest) ]               │
│             │                                          │
│       ┌─────┴─────┐                                    │
│       ▼           ▼                                    │
│  [ Matrix 2A ]  [ Matrix 2B ]                          │
│  iOS Build      Android Build                          │
│  (macos-14)     (ubuntu-latest)                        │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| GitHub Actions Guide | Core Workflow Scope | Key YAML Files & Jobs |
|---|---|---|
| **[README.md](README.md)** | Subsystem index and GitHub Actions pipeline architecture. | Workflow overview. |
| **[workflows.md](workflows.md)** | Production workflow YAML specifications (`release-pipeline.yml`). | Matrix build jobs, triggers, environment secrets. |
| **[action-templates.md](action-templates.md)** | Reusable GitHub Actions workflow templates. | Setup Node, Setup Java/Gradle, Setup Xcode. |

---

# 3. Operational Verification Checklist

- [ ] **`macos-14` Runner Used**: iOS build job executes on `macos-14` or `macos-15` supporting Xcode 16+.
- [ ] **Dependency Caching Active**: Actions cache `node_modules`, `~/.gradle`, and CocoaPods.
- [ ] **Secrets Injected via GitHub**: Secrets referenced via `${{ secrets.APP_STORE_CONNECT_API_KEY }}`.

---

# 4. Official Sources

- GitHub Actions Documentation: https://docs.github.com/en/actions

---

**Last verified:** August 14, 2026

---

# Related documentation

### GitHub Actions

- `release-engineering/github-actions/action-templates.md`
- `release-engineering/github-actions/workflows.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/environment-management.md`

### Signing security

- `signing/security/ci-secrets.md`
