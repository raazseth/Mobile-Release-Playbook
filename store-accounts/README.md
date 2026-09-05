# Store Accounts

Before you can sign, build, or submit anything, you need developer accounts with Apple and Google — the administrative umbrella under which every binary, store listing, API key, and payout lives. This directory covers enrolling in those programs, setting up roles for your team, handling tax and banking, staying on top of legal agreements, and recovering access if something goes wrong.

This directory is **not**:

- a shortcut for getting around Apple's or Google's business identity verification
- an argument for sharing admin passwords or 2FA credentials between teammates
- a substitute for legal counsel when you're accepting a Developer Program Agreement

---

## 1. How the accounts fit together

```text
Legal entity (your company)
        │
        ↓
Apple Developer Program ($99/year)     Google Play Console ($25 one-time)
        │                                       │
        ↓                                       ↓
App Store Connect                      Play Console
(builds, metadata, API keys)           (releases, listings, service accounts)
        │                                       │
        └───────────────┬───────────────────────┘
                         ↓
        Agreements, banking, tax, and payouts
```

## 2. What's in this directory

| File | Covers |
|---|---|
| [apple-developer.md](apple-developer.md) | Enrolling in the Apple Developer Program, account holder setup, D-U-N-S verification, 2FA |
| [google-play-console.md](google-play-console.md) | Setting up Play Console, organization vs. personal accounts, the closed-testing gate |
| [app-store-connect.md](app-store-connect.md) | Managing App Store Connect itself — team invites, API keys, TestFlight |
| [organization-accounts.md](organization-accounts.md) | Individual vs. organization accounts, and how D-U-N-S matching actually works |
| [roles-and-permissions.md](roles-and-permissions.md) | Who should have which role, on both platforms |
| [banking-and-tax.md](banking-and-tax.md) | Paid Applications Agreements, W-8BEN-E/W-9, backup withholding, banking details |
| [agreements.md](agreements.md) | Keeping up with Apple's and Google's developer agreements without getting blocked |
| [account-recovery.md](account-recovery.md) | Transferring account ownership, recovering from lost 2FA, and disaster planning |

## 3. Rules that apply across all of this

- **Use an organization account for anything commercial.** Individual accounts show your personal name as the seller, can't have multiple team roles on Apple's side, and (on Google Play) get stuck behind a mandatory closed-testing period. An organization account needs a D-U-N-S number, but it's worth it for anything beyond a hobby app.
- **Enforce 2FA on every account holder and admin**, ideally with hardware security keys or company-owned devices. Don't enroll a business account with someone's personal Apple ID or Gmail address.
- **Only the account holder can accept updated legal agreements.** Even a full admin can't do this. If the account holder is slow to respond, builds stop shipping — see [agreements.md](agreements.md).
- **Keep API keys out of chat and out of git.** App Store Connect `.p8` keys and Google Play service account JSON keys belong in a managed secrets vault, not Slack, email, or a repo.
- **Make sure your legal name, address, and phone number match exactly** across Dun & Bradstreet, your government business registration, and what you enter in App Store Connect or Play Console. Mismatches are the most common reason enrollment gets rejected or delayed.

---

## Official sources

- Apple Developer Program enrollment: https://developer.apple.com/programs/enroll/
- Google Play identity verification: https://support.google.com/googleplay/android-developer/answer/10788668
- Dun & Bradstreet D-U-N-S lookup: https://developer.apple.com/account/#!/dunsLookup

**Last verified:** September 5, 2026

---

## Related documentation

- `signing/README.md`
- `foundations/mobile-release-lifecycle.md`
- `monetization/README.md`
- `publishing/README.md`
