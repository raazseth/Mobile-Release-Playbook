# Store Metadata Rejection Triage

This document details rejection triage, Apple Guideline 2.3 (Accurate Metadata), Guideline 2.3.7 (Competitor Trademarks), and Google Play call-to-action title violations for **Store Metadata Rejections** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to resolve metadata rejection notices.

This guide is **not**:

- an authorization mechanism to include competitor brand names in keywords
- a guide to keyword stuffing
- a substitute for verifying character limit boundaries

---

# 1. Metadata Rejection Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             METADATA REJECTION DIAGNOSTIC              │
│                                                        │
│  - Inspect rejection message in App Store Connect      │
│    Resolution Center or Google Play Inbox              │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ Guideline 2.3.7 Trademark ]       [ Google Play CTA Title ]
Remove competitor app names from    Remove "Free", "Sale", "Top #1"
keywords & subtitle fields          from App Title & Short Desc
```

---

# 2. Key Metadata Rejections & Resolutions

### Violation 1: Apple Guideline 2.3.7 (Competitor Brand Names in Keywords)
- **Symptom**: Rejection under Guideline 2.3.7 citing trademarked competitor names in keyword field.
- **Resolution**: Edit `keywords.txt` file in Fastlane directory; remove trademarked brand names; resubmit metadata in App Store Connect.

### Violation 2: Google Play Store Listing Policy (Price / CTA in Title)
- **Symptom**: Google Play Console rejects title containing "Free" or "Top #1 App".
- **Resolution**: Edit App Title in Play Console; remove promotional terms; resubmit listing.

---

# 3. Operational Verification Checklist

- [ ] **No Competitor Brands**: Keyword field and subtitle verified free of competitor app names.
- [ ] **No Pricing Claims in Title**: Words like "Free", "Sale", or "Discount" absent from title.
- [ ] **Character Limits Enforced**: Title ≤ 30c, Subtitle ≤ 30c, Keywords ≤ 100c, Short Desc ≤ 80c.

---

# 4. Official Sources

- Apple Guideline 2.3 (Accurate Metadata): https://developer.apple.com/app-store/review/guidelines/#accurate-metadata

---

**Last verified:** August 14, 2026

---

# Related documentation

### Troubleshooting

- `troubleshooting/README.md`
- `troubleshooting/app-not-visible.md`
- `troubleshooting/archive-fails.md`
- `troubleshooting/build-fails.md`
- `troubleshooting/ci-fails.md`
- `troubleshooting/crash-after-release.md`
- `troubleshooting/deep-links-broken.md`
- `troubleshooting/payments-broken.md`
- `troubleshooting/privacy-rejected.md`
- `troubleshooting/push-notifications-broken.md`
- `troubleshooting/review-rejected.md`
- `troubleshooting/signing-fails.md`
- `troubleshooting/upload-fails.md`

### Signing

- `signing/README.md`

### Publishing

- `publishing/README.md`

### Store operations

- `store-operations/rejection-handling.md`

### Post-release

- `post-release/crash-analysis.md`
- `post-release/monitoring.md`

### Testing

- `testing/README.md`
