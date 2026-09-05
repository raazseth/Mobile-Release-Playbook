# Account Recovery

Losing administrative access to your Apple Developer or Google Play Console account stops you from shipping anything — no hotfixes, no support access, nothing. This covers planning for that before it happens, and what to do if it does.

This guide is **not**:

- a way around Apple's or Google's identity security checks
- a suggestion to rely on unverified personal recovery accounts
- a substitute for actually keeping credential backups somewhere safe

---

## 1. What can actually go wrong

- The account holder leaves the company, or changes roles, without a handoff.
- A 2FA hardware key or device gets lost.
- The corporate email domain expires or changes.
- The account is compromised or shows unauthorized activity.

Any of these can leave you locked out of the account that controls your releases — plan for it before it's urgent.

## 2. Transferring the Apple account holder role

When the account holder is leaving or changing roles, transfer ownership deliberately, not at the last minute:

```text
Current Account Holder → Membership details → Update your information → Transfer Account Holder role
        ↓
Choose a candidate from your team, agree to the Account Holder Transferor Agreement
        ↓
Successor gets an email and accepts the transfer
```

(This lives under Membership details, not Users and Access — easy to go looking in the wrong place.)

The successor needs 2FA already enabled, needs to already be an Admin on the account, and should use a company domain email — not a personal one.

## 3. Planning for lost 2FA devices

- Generate Apple and Google account recovery keys ahead of time, and store them in a shared corporate password vault (1Password Teams, Bitwarden Enterprise) that at least two people can access.
- Keep at least **two** people with full Admin access to both App Store Connect and Play Console — never just one.

## 4. If you suspect the account is compromised

1. Log out all active sessions and change the account holder's and admins' passwords immediately.
2. Revoke any `.p8` App Store Connect keys or Google service account JSON keys that might be exposed.
3. Report it to Apple Security or Google Play Developer Support.
4. Check App Store Connect's and Play Console's activity logs for anything unauthorized.

## 5. Before you consider this done

- [ ] At least two people have full Admin access — not just one.
- [ ] 2FA recovery keys are backed up in a shared vault, not on one person's phone.
- [ ] The account holder uses a role-based company email, not a personal one.
- [ ] There's a documented succession plan for the account holder role.
- [ ] You know how to revoke `.p8`/service account keys quickly if you ever need to.

---

## Official sources

- Apple account holder transfer: https://developer.apple.com/help/account/access/transfer-the-account-holder-role
- Google account recovery for businesses: https://support.google.com/a/answer/33561

**Last verified:** September 5, 2026

---

## Related documentation

- `store-accounts/README.md`
- `store-accounts/apple-developer.md`
- `store-accounts/google-play-console.md`
- `store-accounts/roles-and-permissions.md`
