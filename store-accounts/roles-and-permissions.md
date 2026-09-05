# Roles and Permissions

Who should have which role in App Store Connect and Google Play Console — for people and for CI/CD service accounts. The goal is least privilege: everyone and everything gets only the access their job actually needs.

This guide is **not**:

- an argument for giving everyone Admin because it's simpler
- a justification for sharing a single login across multiple people
- a way to get around 2FA on administrative accounts

---

## 1. Why this matters

Over-granting access — a blanket Admin role for every teammate, a CI key with full account access — is how a compromised laptop or a leaked key turns into an unauthorized release or a deleted app. Give people and automated tools the minimum they need, and nothing more. Only the account holder should ever have legal agreement sign-off authority (see [agreements.md](agreements.md)).

## 2. App Store Connect roles

Apple's roles are fixed — you assign one of these to each person, not a custom mix:

| Role | Manage users | Edit metadata | Upload builds | Create API keys | View sales & finance | Accept agreements |
|---|---|---|---|---|---|---|
| Account Holder | Yes | Yes | Yes | Yes | Yes | Yes — exclusively |
| Admin | Yes | Yes | Yes | Yes | Yes | No |
| App Manager | No | Yes (assigned apps) | Yes (assigned apps) | No | No | No |
| Developer | No | No | Yes | No | No | No |
| Finance | No | No | No | No | Yes | No |
| Sales | No | No | No | No | Yes (sales/downloads only) | No |
| Marketing | No | Yes (marketing assets only) | No | No | No | No |
| Customer Support | No | No | No | No | No | No |

Customer Support can still respond to App Store reviews — that's a separate permission from editing metadata, not reflected as a column above.

## 3. Google Play Console permissions

Google Play doesn't use fixed named roles the way Apple does. Below the **Account Owner** (full control) and **Admin** (can manage other users' permissions) levels, you build a user's access out of individual account-level and app-level permission checkboxes — things like *release to production*, *release to testing tracks*, *manage store presence*, *view financial data*, and *reply to reviews*. "Release Manager" or "Store Marketer" aren't real role names in the console — they're just common shorthand for a particular bundle of these checkboxes, so don't go looking for a dropdown with that label.

| Common bundle (not an official role name) | Permissions you'd actually check |
|---|---|
| "Release manager" | Release to production, release to testing tracks, manage testing tracks |
| "Store marketer" | Manage store presence (listings, pricing, promotional content) |
| "Financial analyst" | View financial data, orders, and cancellation survey responses |
| "Support" | Reply to reviews |

## 4. Scoping CI/CD service accounts

Automated tools should get the narrowest access that lets them do their job:

- **Build upload tools (Fastlane, EAS)** — App Manager on App Store Connect; on Play Console, just "release to testing tracks" (add "release to production" only if the pipeline actually deploys straight to prod).
- **Metadata tools** — App Manager or Marketing on Apple; "manage store presence" on Play Console.
- **Status-checking tools** — Developer, or a read-only permission set, on either platform.

## 5. Before you consider this done

- [ ] Admin is restricted to a small number of designated release engineers, not the whole team.
- [ ] There's exactly one active Account Holder per store account.
- [ ] CI/CD keys use App Manager or test-track-only roles, not full Admin.
- [ ] Access to financial reports and banking is restricted to whoever actually handles accounting.
- [ ] Access lists get reviewed periodically to remove departed contractors or employees.

---

## Official sources

- App Store Connect role permissions: https://developer.apple.com/help/app-store-connect/reference/role-permissions/
- Play Console user permissions: https://support.google.com/googleplay/android-developer/answer/9844686

**Last verified:** September 5, 2026

---

## Related documentation

- `store-accounts/README.md`
- `store-accounts/app-store-connect.md`
- `store-accounts/google-play-console.md`
- `ai/security/agent-permissions.md`
