# Metadata Validation Automation Subsystem Handbook

This directory contains production handbook guides, CLI scripts, and character-limit validation tools for **Metadata Validation** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to validate store metadata text files and URLs before submission.

This guide is **not**:

- an authorization mechanism to submit metadata exceeding platform character limits
- a substitute for checking Guideline 2.3.7 competitor brand term bans
- a guide to submitting broken support or privacy policy URLs

---

# 1. Metadata Validation Pipeline Architecture

Metadata validation parses local metadata JSON/text files and asserts compliance against store character limits and policy guidelines.

```text
┌────────────────────────────────────────────────────────┐
│             METADATA VALIDATION PIPELINE               │
│                                                        │
│  [ Local Metadata Files (`metadata.json`) ]            │
│                         │                              │
│                         ▼                              │
│  - App Title Length        ──→ Max 30 Characters       │
│  - Subtitle Length         ──→ Max 30 Characters       │
│  - Keywords Length         ──→ Max 100 Characters      │
│  - Competitor Brand Scan   ──→ Guideline 2.3.7 Ban     │
│  - URL HTTP 200 Status Check ──→ Privacy Policy URL    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Pass (Exit 0) ──→ Metadata Ready for Store Upload ]
```

---

# 2. Key Automation Handbooks

- **[validate-metadata.md](validate-metadata.md)**: Automated metadata compliance script guide & implementation.

---

# 3. Operational Verification Checklist

- [ ] **Title <= 30c**: App Title verified <= 30 characters.
- [ ] **Subtitle <= 30c**: Subtitle verified <= 30 characters.
- [ ] **No Competitor Brands**: Text scanned free of forbidden trademarked brand names.

---

# 4. Official Sources

- App Store Review Guidelines (Guideline 2.3): https://developer.apple.com/app-store/review/guidelines/#product-page

---

**Last verified:** August 14, 2026
