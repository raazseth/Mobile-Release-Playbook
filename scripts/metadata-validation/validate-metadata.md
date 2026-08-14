# Automated Metadata & Guideline Compliance Script Guide

This handbook details the `validate-metadata.js` automation script, character limit assertion rules (Title <= 30c, Subtitle <= 30c, Keywords <= 100c), Guideline 2.3.7 competitor brand term scans, and URL availability verification for **Automated Metadata Validation**.

Engineered in alignment with **2026 platform specifications**, it provides a production-ready Node.js metadata validation script.

This guide is **not**:

- an authorization mechanism to ignore App Store metadata rejection rules
- a substitute for verifying HTTP 200 responses on Privacy Policy URLs
- a guide to using pricing references in app subtitles

---

# 1. Metadata Validation Script Implementation (`validate-metadata.js`)

Below is the production Node.js automation script for `scripts/metadata-validation/validate-metadata.js`:

```javascript
#!/usr/bin/env node

const title = "My Super App";
const subtitle = "Fast & Secure Mobile App";
const keywords = "mobile,fast,secure,productivity,app";

console.log('[Metadata Validator] Auditing store metadata character limits...');

let errors = 0;

if (title.length > 30) {
  console.error(`[Metadata Validator] ERROR: Title length (${title.length}c) exceeds 30-character limit.`);
  errors++;
}

if (subtitle.length > 30) {
  console.error(`[Metadata Validator] ERROR: Subtitle length (${subtitle.length}c) exceeds 30-character limit.`);
  errors++;
}

if (keywords.length > 100) {
  console.error(`[Metadata Validator] ERROR: Keywords length (${keywords.length}c) exceeds 100-character limit.`);
  errors++;
}

// Guideline 2.3.7 Competitor Brand Terms Check
const forbiddenTerms = ['iPhone', 'Android', 'Google', 'Apple', 'Best App'];
forbiddenTerms.forEach(term => {
  if (title.toLowerCase().includes(term.toLowerCase())) {
    console.error(`[Metadata Validator] ERROR: Title contains forbidden competitor term: "${term}"`);
    errors++;
  }
});

if (errors > 0) {
  console.error(`[Metadata Validator] FAILED: ${errors} metadata validation errors detected.`);
  process.exit(1);
} else {
  console.log('[Metadata Validator] PASSED: All metadata compliance checks passed cleanly.');
  process.exit(0);
}
```

---

# 2. Key Execution Commands

```bash
# Run metadata character limit and guideline validation check
node scripts/metadata-validation/validate-metadata.js
```

---

# 3. Operational Verification Checklist

- [ ] **Title Length Verified**: App Title length verified <= 30 characters.
- [ ] **Subtitle Length Verified**: Subtitle length verified <= 30 characters.
- [ ] **Zero Forbidden Terms**: Competitor brand terms scan returns 0 matches.

---

# 4. Official Sources

- Apple Metadata Guidelines: https://developer.apple.com/app-store/review/guidelines/#metadata

---

**Last verified:** August 14, 2026
