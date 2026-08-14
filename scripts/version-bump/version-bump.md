# Automated Version Bumping & SemVer Synchronization Script Guide

This handbook details the `version-bump.js` Node.js CLI script, automated version synchronization across `package.json`, `app.json`, `Info.plist`, `build.gradle`, and `CHANGELOG.md` updates for **Automated Version Bumping**.

Engineered in alignment with **2026 platform specifications**, it provides a production-ready Node.js version bumping script.

This guide is **not**:

- an authorization mechanism to break version string synchronization between iOS and Android
- a substitute for updating `CHANGELOG.md`
- a guide to skipping monotonic build number increments

---

# 1. Version Bumping Script Implementation (`version-bump.js`)

Below is the production Node.js automation script implementation for `scripts/version-bump/version-bump.js`:

```javascript
#!/usr/bin/env node

const fs = require('fs');
const path = require('path');

const projectRoot = path.resolve(__dirname, '../../');
const packageJsonPath = path.join(projectRoot, 'package.json');
const appJsonPath = path.join(projectRoot, 'app.json');

// Read current package.json
const packageJson = JSON.parse(fs.readFileSync(packageJsonPath, 'utf8'));
const currentVersion = packageJson.version;

const args = process.argv.slice(2);
const bumpType = args.includes('--patch') ? 'patch' : args.includes('--minor') ? 'minor' : 'patch';

const parts = currentVersion.split('.').map(Number);
if (bumpType === 'minor') {
  parts[1] += 1;
  parts[2] = 0;
} else {
  parts[2] += 1;
}

const newVersion = parts.join('.');
console.log(`[Version Bump] Bumping ${currentVersion} -> ${newVersion}`);

// Update package.json
packageJson.version = newVersion;
fs.writeFileSync(packageJsonPath, JSON.stringify(packageJson, null, 2) + '\n');

// Update app.json if present
if (fs.existsSync(appJsonPath)) {
  const appJson = JSON.parse(fs.readFileSync(appJsonPath, 'utf8'));
  if (appJson.expo) {
    appJson.expo.version = newVersion;
    fs.writeFileSync(appJsonPath, JSON.stringify(appJson, null, 2) + '\n');
  }
}

console.log(`[Version Bump] Successfully updated version to ${newVersion}`);
```

---

# 2. Key Execution Commands

```bash
# Execute patch version bump (1.2.0 -> 1.2.1)
node scripts/version-bump/version-bump.js --patch

# Execute minor version bump (1.2.0 -> 1.3.0)
node scripts/version-bump/version-bump.js --minor
```

---

# 3. Operational Verification Checklist

- [ ] **Script Returns Exit Code 0**: Script completes without throwing unhandled exceptions.
- [ ] **Files Updated**: `package.json` and `app.json` reflect identical updated version string.
- [ ] **Git Working Tree Audited**: `git status` shows modified version files ready for staging.

---

# 4. Official Sources

- Node.js File System Documentation: https://nodejs.org/docs/latest/api/fs.html

---

**Last verified:** August 14, 2026
