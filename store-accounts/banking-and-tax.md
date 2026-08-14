# Banking, Tax Setup & US Backup Withholding

This document details the financial setup, banking routing (IBAN/SWIFT), tax forms (W-8BEN-E for foreign legal entities, W-9 for US entities), US backup withholding defense (24%), and payout threshold administration for **Apple Developer Program & Google Play Console Accounts**.

Engineered in alignment with **2026 platform requirements**, it specifies how to complete paid applications tax and banking setups to receive app store earnings without facing financial holds or unexpected tax withholdings.

This guide is **not**:

- formal tax or legal accounting advice (consult a certified CPA or tax attorney for entity tax filings)
- a guide to using personal bank accounts for corporate app payouts
- a substitute for submitting valid US IRS tax forms

---

# 1. Financial Setup Architecture & Payout Flow

To sell paid applications, in-app purchases, or auto-renewable subscriptions, developer accounts MUST accept the Paid Applications Agreement and complete tax and banking setups.

```text
[ Customer Purchases In-App Item ]
               │
               ▼
┌────────────────────────────────────────────────────────┐
│            STORE TRANSACTION PROCESSING                │
│  - Apple / Google collects payment & remits local VAT  │
│  - Stores commission (15% to 30%) deducted            │
└──────────────────────────┬─────────────────────────────┘
               │
               ▼
┌────────────────────────────────────────────────────────┐
│            TAX WITHHOLDING CHECK (US IRS)              │
│  - Valid W-8BEN-E / W-9 Form on file?                  │
│  - Yes → Standard payout (0% US backup withholding)    │
│  - No  → Mandatory 24% US Backup Withholding Deducted  │
└──────────────────────────┬─────────────────────────────┘
               │
               ▼
┌────────────────────────────────────────────────────────┐
│            MONTHLY FINANCIAL PAYOUT (SWIFT / IBAN)     │
│  - Dispatched to corporate bank account upon threshold  │
└────────────────────────────────────────────────────────┘
```

---

# 2. US IRS Tax Form Setup (W-9 & W-8BEN-E)

Both Apple and Google require developers to submit US IRS tax forms to determine US withholding tax applicability on global sales:

### 2.1 US Legal Entities (Form W-9)

US corporations and LLCs submit Form W-9 providing their Employer Identification Number (EIN). US entities are not subject to US backup withholding if a valid EIN is verified.

### 2.2 Non-US Foreign Legal Entities (Form W-8BEN-E)

Foreign organizations selling to US storefront customers must complete Form W-8BEN-E:

- **Chapter 3 Status**: Claim Active NFFE (Non-Financial Foreign Entity) status or appropriate entity type.
- **Tax Treaty Claim (Part III)**: Claim tax treaty benefits between your country of residence and the United States to reduce or eliminate US withholding tax on digital royalties.
- **Foreign TIN**: Provide your home country Tax Identification Number (TIN).

> **CRITICAL FINANCIAL RISK**: Failing to complete Form W-8BEN-E or providing invalid tax information will cause Apple and Google to deduct a mandatory **24% US Backup Withholding Tax** from all US storefront revenue.

---

# 3. Banking Routing & Currency Requirements

1. **Bank Account Ownership**: The bank account name MUST match the legal entity name registered on the developer account identically.
2. **SWIFT / IBAN Details**: Provide complete SWIFT/BIC code, IBAN or account number, and bank branch address.
3. **Payout Minimum Thresholds**: Apple and Google enforce monthly payout thresholds (typically $150 USD equivalent). Earnings below the threshold roll over to the next monthly cycle.

---

# 4. Operational Verification Checklist

- [ ] **Paid Applications Agreement Signed**: Active Paid Applications Agreement signed in App Store Connect / Play Console.
- [ ] **W-8BEN-E / W-9 Verified**: IRS tax form submitted and status marked as "Approved" or "Complete".
- [ ] **Backup Withholding Avoided**: 0% US backup withholding confirmed on financial statements.
- [ ] **Bank Name Matched**: Bank account holder name matches corporate legal entity name identically.
- [ ] **SWIFT / IBAN Validated**: Bank routing details verified by receiving financial institution.

---

# 5. Related Documentation

- [Apple Developer Handbook](apple-developer.md) - Program setup.
- [Google Play Console Handbook](google-play-console.md) - Play Console setup.
- [Agreements Handbook](agreements.md) - License agreements.

---

# 6. Official Sources

- Apple App Store Connect Banking Help: https://developer.apple.com/help/app-store-connect/#/dev02d0cf318
- IRS Form W-8BEN-E Instructions: https://www.irs.gov/forms-pubs/about-form-w-8ben-e
- Google Play Payout Setup: https://support.google.com/googleplay/android-developer/answer/7161426

---

**Last verified:** August 14, 2026
