# Apple Developer Program

This covers enrolling in the Apple Developer Program as an organization: what you need before you start, how the verification call works, and what to watch for on renewal.

This guide is **not**:

- an App Store Connect administration guide (see [app-store-connect.md](app-store-connect.md))
- a guide to generating signing certificates (see [signing/](../signing/README.md))
- a substitute for reading Apple's own legal entity documentation

---

## 1. What the program gets you

Apple Developer Program membership costs $99/year and gives you access to the iOS, iPadOS, macOS, watchOS, and tvOS SDKs, beta software, App Store Connect, and native capabilities like Associated Domains, Sign in with Apple, HealthKit, and push notifications. For anything beyond a personal side project, enroll as an **organization**, not an individual — it verifies your business rather than a person, and it's what lets you add teammates with different roles later (see [roles-and-permissions.md](roles-and-permissions.md)).

## 2. Before you start enrollment

You'll need all of this in place before Apple will approve an organization account:

1. **A corporate Apple Account with 2FA enabled** — use a company email address (e.g. `apple-developer@company.com`), not a personal one, and turn on two-factor authentication.
2. **A D-U-N-S number** — a 9-digit business identifier from Dun & Bradstreet, tied to your actual legal entity (corporation, LLC, etc.). Apple rejects DBAs, trade names, and branch offices — it has to be the entity itself.
3. **Legal entity status** — the company has to be able to legally enter into contracts with Apple.
4. **Legal signing authority** — whoever enrolls needs to actually be the owner, an executive, or have explicit authority delegated by one. Apple verifies this by phone.
5. **A public company website**, hosted on a domain that matches your company name.

## 3. How the verification actually happens

```text
Submit enrollment (Developer app or web)
        ↓
Apple checks your legal entity name and address against Dun & Bradstreet
        ↓
Apple calls the phone number on file in D-U-N-S
  to confirm the person enrolling has legal authority
        ↓
Approved → pay the $99 annual fee → access granted
```

If the phone number Apple has on file for your D-U-N-S record is disconnected or unreachable, this is where enrollment usually stalls — worth checking before you submit.

## 4. Renewal

Membership renews annually at $99. If it lapses, your App Store listings drop out of search, TestFlight builds stop working, and push notifications fail — so it's worth turning on auto-renewal in App Store Connect (Account → Membership) against a card that won't expire unexpectedly.

## 5. Before you consider this done

- [ ] Account holder uses a company-owned email address, not a personal one.
- [ ] 2FA is enabled on the account holder's credentials.
- [ ] The company name entered matches the Dun & Bradstreet record exactly.
- [ ] Auto-renewal is turned on with an active card behind it.
- [ ] There's a documented plan for transferring account holder status if that person leaves — see [account-recovery.md](account-recovery.md).

---

## Official sources

- Apple Developer Program enrollment: https://developer.apple.com/programs/enroll/
- D-U-N-S lookup tool: https://developer.apple.com/account/#!/dunsLookup

**Last verified:** September 5, 2026

---

## Related documentation

- `store-accounts/README.md`
- `store-accounts/app-store-connect.md`
- `store-accounts/organization-accounts.md`
- `store-accounts/agreements.md`
- `store-accounts/account-recovery.md`
