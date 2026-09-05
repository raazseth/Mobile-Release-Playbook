# Organization vs. Individual Accounts

This covers the actual difference between an individual and an organization developer account on Apple and Google, and — since this is where enrollment most often goes wrong — how D-U-N-S identity matching works and what causes rejections.

This guide is **not**:

- an argument for registering a fake or fictitious business entity
- a way to move a personal account to an organization without full verification
- a substitute for checking your own Dun & Bradstreet record before you enroll

---

## 1. Individual vs. organization

| | Individual account | Organization account |
|---|---|---|
| Public seller name | Your personal name | Your legal business name |
| Team roles | Single user (Apple) | Full role-based access for multiple people |
| Verification | Personal ID check | D-U-N-S number + business registration |
| Google Play closed testing | Mandatory: 12 testers, 14 days | Exempt — direct production access |
| Liability | Personal | Limited to the entity |

If you're publishing anything commercial, use an organization account. It's more setup up front, but it's the only way to add teammates with distinct roles, and it skips Google Play's closed-testing gate entirely.

## 2. Why D-U-N-S matching is where enrollment usually breaks

Apple and Google both check the legal name, address, and phone number you enter against your Dun & Bradstreet record — and they check for an exact match, not something close enough.

The most common ways this goes wrong:

- **Using a DBA or trade name** instead of the legal entity name — e.g. enrolling as "Acme Apps" when your D-U-N-S record says "Acme Technologies Corporation."
- **Address formatting mismatches** — "Suite 400" in one place and "Ste 400" in the other is enough to fail an automated match.
- **An unreachable phone number** — Apple calls the number on file in D-U-N-S to verify the account holder's authority. If that number is disconnected, verification stalls.

Check your D-U-N-S record against exactly what you plan to type into the enrollment form before you submit — it saves a round trip.

## 3. Legal authority to enroll

Whoever enrolls the organization needs to actually be authorized to bind it — the owner, an executive (CEO, CTO, CFO), or someone with explicit authority delegated by one. Apple confirms this with a phone call to the number in your D-U-N-S record, so make sure whoever answers that line knows what the call is about.

## 4. Before you consider this done

- [ ] You've enrolled as an organization, not an individual, for a commercial app.
- [ ] Your legal name and address match your D-U-N-S record exactly, including formatting.
- [ ] The account holder's email is on your company domain, not personal.
- [ ] The phone number in your D-U-N-S record is live and someone will actually answer Apple's verification call.
- [ ] The bank account for payouts is in the legal entity's name, not an individual's.

---

## Official sources

- Apple organization enrollment: https://developer.apple.com/support/enrollment/
- Dun & Bradstreet D-U-N-S overview: https://www.dnb.com/duns-number.html

**Last verified:** September 5, 2026

---

## Related documentation

- `store-accounts/README.md`
- `store-accounts/apple-developer.md`
- `store-accounts/google-play-console.md`
- `store-accounts/agreements.md`
