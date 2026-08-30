# Reusable GitHub Actions Workflow Templates Handbook

This handbook details reusable composite action templates (`.github/actions/setup-mobile-env`), Node.js environment setup, Java 17 / Gradle setup, Xcode 16 setup, and caching actions for **GitHub Actions Templates** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to modularize GitHub Actions CI/CD workflows.

This guide is **not**:

- an authorization mechanism to duplicate setup steps across multiple workflow files
- a substitute for using official GitHub Action actions (`actions/setup-java@v4`, `actions/setup-node@v4`)
- a guide to un-cached setup actions

---

# 1. Composite Environment Setup Template (`action.yml`)

Create a reusable composite action at `.github/actions/setup-mobile-env/action.yml`:

```yaml
name: 'Setup Mobile Environment'
description: 'Sets up Node.js, npm caching, and installs project dependencies'

runs:
  using: 'composite'
  steps:
    - uses: actions/setup-node@v4
      with:
        node-version: '20'
        cache: 'npm'

    - name: Restore npm Cache
      uses: actions/cache@v4
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-

    - name: Install Dependencies
      shell: bash
      run: npm ci
```

---

# 2. Key Setup Templates

- **Node.js Template**: `actions/setup-node@v4` with Node 20 LTS and `npm` cache.
- **Java / Android Template**: `actions/setup-java@v4` with Zulu JDK 17 and Gradle caching.
- **Ruby / Fastlane Template**: `ruby/setup-ruby@v1` with Bundler caching.

---

# 3. Operational Verification Checklist

- [ ] **Composite Action Formatted**: `action.yml` uses `using: 'composite'`.
- [ ] **JDK 17 Configured**: Java setup uses Zulu distribution version 17.
- [ ] **Cache Keys Hash Lockfiles**: Cache keys include `hashFiles('**/package-lock.json')`.

---

# 4. Official Sources

- GitHub Actions Creating Composite Actions: https://docs.github.com/en/actions/creating-actions/creating-a-composite-action

---

**Last verified:** August 14, 2026

---

# Related documentation

### GitHub Actions

- `release-engineering/github-actions/README.md`
- `release-engineering/github-actions/workflows.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/environment-management.md`

### Signing security

- `signing/security/ci-secrets.md`
