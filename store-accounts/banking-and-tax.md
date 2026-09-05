# Banking and Tax

This covers what you actually need to set up to get paid for paid apps, in-app purchases, or subscriptions — tax forms, banking details, and the withholding trap that catches people who skip the tax form.

This guide is **not**:

- tax or legal advice — talk to a CPA or tax attorney about your entity's actual filings
- a guide to using a personal bank account for a company's app revenue
- a substitute for submitting a real, valid IRS tax form

---

## 1. How a purchase turns into a payout

```text
Customer buys something in your app
        ↓
Apple/Google processes the payment, remits local VAT, takes their commission (15-30%)
        ↓
Do you have a valid tax form on file?
  Yes → normal payout
  No  → 24% US backup withholding deducted
        ↓
Payout sent to your bank account once you hit the monthly threshold
```

## 2. The tax form you actually need

Both platforms require a US tax form to determine how much (if any) US withholding applies to your earnings — even if your company isn't American.

**US entities** file a **W-9** with their EIN. Verified, on file, no backup withholding.

**Non-US entities** file a **W-8BEN-E**:

- Claim your entity type (usually Active NFFE — a non-financial foreign entity).
- Claim any tax treaty between your country and the US, if one applies — it can reduce or eliminate withholding on digital royalties.
- Provide your home country's tax ID.

> **Important:** Skip this form, or get it wrong, and Apple and Google will withhold **24%** of your US storefront revenue automatically. This is the single most common way developers unexpectedly lose a chunk of their payout.

In App Store Connect, both the Paid Applications Agreement and the tax form live in the same place: **Business (homepage) → Agreements tab → sign the Paid Apps agreement first, then find the Tax Forms section → Add Tax Info** next to the form you need. Apple asks a few questions to route you to the right W-8 variant (BEN, BEN-E, or ECI) automatically — you don't pick the form yourself. Once submitted, you can't edit it in App Store Connect; you'd need to file a correction instead.

## 3. Banking details

- Your bank account name has to match your legal entity name exactly — a personal account won't work for a corporate payout.
- You'll need a full SWIFT/BIC code, IBAN or account number, and branch address.
- Payouts have a minimum threshold, and it's lower than most people expect: Apple's is currency-dependent — as low as $0.02 for USD, EUR, GBP, CAD, AUD, and JPY, and $40 USD for currencies not on that list. Google Play's is $1 for local-currency payout or $100 USD for a wire transfer. Either way, earnings below the threshold just roll into the next month rather than triggering a payout.

## 4. Before you consider this done

- [ ] The Paid Applications Agreement is signed in App Store Connect / Play Console.
- [ ] Your W-8BEN-E or W-9 shows as approved/complete, not pending.
- [ ] Your payout statements show 0% US backup withholding.
- [ ] The bank account name matches your legal entity name exactly.
- [ ] Your bank has confirmed the SWIFT/IBAN details are correct.

---

## Official sources

- App Store Connect banking help: https://developer.apple.com/help/app-store-connect/#/dev02d0cf318
- Apple minimum payment threshold (by currency): https://developer.apple.com/help/app-store-connect/reference/minimum-payment-threshold
- IRS Form W-8BEN-E instructions: https://www.irs.gov/forms-pubs/about-form-w-8ben-e
- Google Play payout setup: https://support.google.com/googleplay/android-developer/answer/7161426

**Last verified:** September 5, 2026

---

## Related documentation

- `store-accounts/README.md`
- `store-accounts/apple-developer.md`
- `store-accounts/google-play-console.md`
- `store-accounts/agreements.md`
