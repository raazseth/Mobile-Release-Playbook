# App Store Connect

App Store Connect is Apple's portal (and API) for everything after enrollment: uploading builds, managing metadata, running TestFlight, configuring in-app purchases, and viewing sales reports. This covers administering the portal itself — inviting people, generating API keys — not the release mechanics that happen inside it.

This guide is **not**:

- an argument for sharing an App Store Connect API key over Slack or email
- a replacement for the account holder actually accepting agreements (see [agreements.md](agreements.md))
- a walkthrough of TestFlight itself — see [publishing/ios/testflight.md](../publishing/ios/testflight.md)

---

## 1. What App Store Connect manages

- App metadata and store listings
- Build uploads and TestFlight distribution
- In-app purchase and subscription configuration
- API keys, for automated tools like Fastlane or EAS Submit
- Financial agreements, tax forms, and payout reports

Human teammates get role-based access (see [roles-and-permissions.md](roles-and-permissions.md)); CI/CD tools authenticate through API keys instead.

## 2. API keys for automated tools

Fastlane, EAS Submit, and any script that talks to the App Store Connect API need an API key, not a personal login:

```text
Users and Access → Keys → Generate API Key
        ↓
Download the .p8 key file — this is the only chance you get
        ↓
Store it in a managed CI secrets vault
```

A key has a **Key ID** (10 characters, e.g. `2X9R49336D`) and belongs to your account's **Issuer ID** (a UUID). Give it the **App Manager** role for build deployment, or **Developer** if the tool only needs to read status.

> **Note:** Apple lets you download the `.p8` private key exactly once. If you lose it, you have to revoke it and generate a new one — there's no re-download. Store it immediately, and never commit it to a repository.

## 3. TestFlight, briefly

App Store Connect is also where TestFlight lives — internal testing for your own team (instant, up to 100 people) and external testing for real beta users (requires Apple's Beta App Review, up to 10,000 people). The full workflow, including build expiration and crash feedback, is in [publishing/ios/testflight.md](../publishing/ios/testflight.md).

## 4. Before you consider this done

- [ ] `.p8` keys live only in CI secrets, never in a repo commit.
- [ ] CI keys use App Manager rather than full Admin where that's enough.
- [ ] `ITSAppUsesNonExemptEncryption` is declared in `Info.plist` so TestFlight doesn't prompt for export compliance on every build.
- [ ] Sandbox testers exist for testing in-app purchases (**Users and Access → Sandbox tab → + → Create Test Accounts**; use an email that's never been used as a real Apple Account, and note you can't edit the name/email/password after creation).
- [ ] Everyone invited to the team has 2FA enabled on their Apple Account.

---

## Official sources

- App Store Connect help: https://developer.apple.com/help/app-store-connect/
- Creating API keys: https://developer.apple.com/documentation/appstoreconnectapi/creating_api_keys_for_app_store_connect_api

**Last verified:** September 5, 2026

---

## Related documentation

- `store-accounts/README.md`
- `store-accounts/apple-developer.md`
- `store-accounts/roles-and-permissions.md`
- `publishing/ios/testflight.md`
- `ai/security/secret-protection.md`
