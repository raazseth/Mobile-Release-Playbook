# Store Metadata & Guidelines Compliance Audit Handbook

This handbook details metadata audits, App Title 30c limit, Subtitle 30c limit, Keywords 100c limit, Guideline 2.3.7 competitor trademark bans, and Google Play call-to-action rules for **Metadata Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit store listing text to prevent metadata rejections.

This guide is **not**:

- an authorization mechanism to include competitor brand names in keywords
- a substitute for checking character limit boundaries
- a guide to keyword stuffing

---

# 1. Store Metadata Limits Audit Schema

```text
┌────────────────────────────────────────────────────────┐
│             STORE METADATA AUDIT SCHEMA                │
│                                                        │
│  - App Title: ≤ 30 Characters (iOS & Android)          │
│  - Subtitle: ≤ 30 Characters (iOS)                     │
│  - Short Description: ≤ 80 Characters (Android)        │
│  - Keywords: ≤ 100 Characters (iOS, Comma-Separated)   │
│  - Full Description: ≤ 4000 Characters                 │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Free of Competitor Trademarks & Promotional Claims ]
```

---

# 2. Key Metadata Audit Rules

- **Apple Guideline 2.3.7 Ban**: Keyword field and subtitle MUST NOT contain trademarked competitor app names (e.g., "Strava", "Nike Run").
- **Google Play Promotional Text Ban**: App Title and Short Description MUST NOT contain terms like "Free", "Sale", or "Top #1 App".
- **Release Notes Clarity**: Release notes MUST describe actual changes ("Bug fixes for workout timer log") rather than generic text ("General improvements").

---

# 3. Operational Verification Checklist

- [ ] **Character Limits Respected**: Title ≤ 30c, Subtitle ≤ 30c, Keywords ≤ 100c, Short Desc ≤ 80c.
- [ ] **Competitor Trademarks Audited**: Zero competitor brand names present in keywords or subtitle.
- [ ] **Release Notes Meaningful**: Release notes provide clear, specific details about build updates.

---

# 4. Official Sources

- Apple Guideline 2.3 (Accurate Metadata): https://developer.apple.com/app-store/review/guidelines/#accurate-metadata

---

**Last verified:** August 14, 2026
