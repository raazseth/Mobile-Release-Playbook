# Google Play Console

This covers setting up a Google Play Console developer account: choosing organization vs. personal, what verification actually requires, and the closed-testing gate personal accounts get stuck behind.

This guide is **not**:

- an argument for sharing a Google account's root password across a team
- a way around the 12-tester closed-testing requirement for personal accounts
- a substitute for verifying your Google Payments profile is set up correctly

---

## 1. Organization vs. personal accounts

| | Personal account | Organization account |
|---|---|---|
| Verification | Individual identity check | D-U-N-S number + business registration documents |
| Production access | Blocked behind closed testing (see below) | Direct, once verified |
| Registration fee | $25 one-time | $25 one-time |
| Recommended for | Solo hobby projects | Anything commercial |

If you're publishing a commercial app, use an organization account — it skips the closed-testing gate entirely and gives you a real basis for adding teammates with scoped roles later.

## 2. Setting up an organization account

1. **D-U-N-S number** — the same 9-digit Dun & Bradstreet identifier Apple asks for. Your business name and address need to match your Google Payments profile exactly.
2. **Official business documents** — articles of incorporation, a business license, or a tax registration certificate.
3. **Google Search Console verification** — you'll need to verify ownership of your company's website.
4. **$25 one-time registration fee**, paid by card.

## 3. The closed-testing gate (personal accounts only)

Personal developer accounts created after November 13, 2023 have to clear a mandatory testing period before Google unlocks production access. Organization accounts, and personal accounts created before that date, are exempt.

```text
Upload an .aab to a closed testing track
        ↓
Recruit 12 opted-in testers
        ↓
Keep all 12 actively opted in, overlapping, for 14 continuous days
        ↓
Apply for production access → subject to manual review
```

> **Note:** Google checks that testers are genuinely active, not just opted in, and the 12 have to overlap within the same 14-day window. If one drops out on day 7, you don't meet the requirement anymore — Google resets the clock rather than letting you back-fill. Recruit a few more than 12 to give yourself margin. (The tester count itself dropped from 20 to 12 in December 2024, so don't trust older guides that still say 20.)

This is the main reason to use an organization account for anything commercial — it bypasses this gate entirely.

## 4. Service accounts for automated deployment

Tools like Fastlane Supply or EAS Submit authenticate to Play Console through a Google Cloud service account, not a personal login:

```text
Google Cloud Console → create a service account
        ↓
Grant it access in Play Console (scoped to what it actually needs)
        ↓
Export the JSON key → store it in your CI secrets vault
```

Scope the service account down to what the job actually does — release to testing tracks, or manage store listings — rather than granting it broad access. And keep the JSON key out of the repo.

## 5. Before you consider this done

- [ ] Organization account, D-U-N-S number and business documents approved.
- [ ] Your website is verified in Google Search Console.
- [ ] The deployment service account's JSON key lives in your CI secrets vault, not in git.
- [ ] Your public developer contact email and address are current in Play Console settings.
- [ ] 2-Step Verification is on for every Google account with Play Console access.

---

## Official sources

- Play Console account verification: https://support.google.com/googleplay/android-developer/answer/10788668
- Closed testing requirements: https://support.google.com/googleplay/android-developer/answer/9845334

**Last verified:** September 5, 2026

---

## Related documentation

- `store-accounts/README.md`
- `store-accounts/apple-developer.md`
- `store-accounts/organization-accounts.md`
- `store-accounts/roles-and-permissions.md`
