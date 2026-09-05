# Developer Agreements

Apple and Google periodically update their developer agreements — the Apple Developer Program License Agreement (DPLA) and the Google Play Developer Distribution Agreement (DDA). This covers what happens when they do, and why letting one sit unaccepted blocks releases.

This guide is **not**:

- an argument for auto-accepting legal agreements without anyone actually reading them
- legal advice — have counsel review anything substantive
- something you can delegate to a regular Admin or Developer role

---

## 1. What happens when an agreement updates

Apple and Google update these agreements to reflect new regulation (the EU's Digital Markets Act, regional billing rules), new platform capabilities, or fee changes.

```text
Apple/Google publishes an updated agreement
        ↓
App Store Connect / Play Console shows a banner, and emails the account holder
        ↓
Account holder accepts → nothing changes, releases continue normally
Account holder ignores it → builds get blocked, TestFlight halts, listing edits fail
```

## 2. Only the account holder can accept it

This is exclusive to the **Account Holder / Owner** role — a full Admin cannot click "I Agree" on an updated DPLA or DDA, no matter how much other access they have. If your account holder is slow to check email, that's a real release risk, not just an inconvenience.

## 3. What gets blocked if it's ignored

- External TestFlight builds can't be submitted for Beta App Review.
- Binary submissions to production tracks on either store fail outright.
- Store listing edits — pricing, descriptions, screenshots — get rejected.

## 4. Before you consider this done

- [ ] Someone is actually monitoring the account holder's email for agreement notices, not just assuming they'll see it.
- [ ] Updated agreements get reviewed and accepted within a few business days.
- [ ] App Store Connect's Agreements, Tax, and Banking page shows "Active" for everything.
- [ ] Your CI/CD pipeline isn't silently failing on an agreement-related error nobody's noticed.

---

## Official sources

- Apple Developer Program License Agreement: https://developer.apple.com/terms/
- Google Play Developer Distribution Agreement: https://play.google.com/about/developer-distribution-agreement.html

**Last verified:** September 5, 2026

---

## Related documentation

- `store-accounts/README.md`
- `store-accounts/apple-developer.md`
- `store-accounts/google-play-console.md`
- `store-accounts/roles-and-permissions.md`
