# Social Authentication & OAuth Integration

This guide details the integration architecture, Sign in with Apple mandatory rules (Apple Guideline 4.0), Google Sign-In, OAuth 2.0 PKCE authorization flows, and secure token storage mechanisms for **OAuth & Social Authentication** in React Native and Expo applications.

It specifies how to implement social login while ensuring strict compliance with store review guidelines and platform security standards.

This guide is **not**:

- an authorization mechanism to store plain-text access tokens in `AsyncStorage`
- a bypass for Apple Guideline 4.0 Sign in with Apple requirements
- a custom URL scheme authentication handler (see [universal-links/](../universal-links/README.md))

---

# 1. Integration Architecture & OAuth 2.0 PKCE Flow

Mobile OAuth implementations MUST use the **OAuth 2.0 Authorization Code Grant with Proof Key for Code Exchange (PKCE)** to prevent authorization code interception.

```text
[ User Taps "Sign In with Google / Apple" ]
                   │
                   ▼
[ Generate Cryptographic PKCE Code Verifier & Challenge ]
                   │
                   ▼
┌────────────────────────────────────────────────────────┐
│            SECURE SYSTEM BROWSER SIGHT                 │
│  (iOS `ASWebAuthenticationSession` / Android Custom Tabs)
└──────────────────────────┬─────────────────────────────┘
                   │
                   ▼ (User Authenticates & Returns Code)
┌────────────────────────────────────────────────────────┐
│            TOKEN EXCHANGE & SECURE STORAGE             │
│  - Exchange Auth Code + PKCE Verifier for Tokens       │
│  - Save Tokens in iOS Keychain / Android Keystore      │
└────────────────────────────────────────────────────────┘
```

---

# 2. Apple App Store Guideline 4.0 Enforcement

Apple Guideline 4.0 strictly mandates Sign in with Apple requirements:

```text
┌────────────────────────────────────────────────────────┐
│             APPLE GUIDELINE 4.0 MANDATE                │
│                                                        │
│   If your application uses any third-party social      │
│   login service (such as Google, Facebook, Google Sign-In,│
│   or X), you MUST ALSO offer Sign in with Apple as an  │
│   equivalent option to users.                          │
└────────────────────────────────────────────────────────┘
```

### Exceptions to Guideline 4.0

Sign in with Apple is NOT required if:

- Your app exclusively uses your company's own primary account setup and password system.
- Your app is an education, enterprise, or business app that requires logging in with an existing corporate/institutional account.
- Your app uses a citizen identification system or electronic ID to authenticate users.

---

# 3. Secure Token Storage (Keychain & Keystore)

Access tokens, refresh tokens, and session credentials MUST NEVER be stored in un-encrypted storage (`AsyncStorage`, `localStorage`, or raw files).

### Expo SecureStore / Keychain Implementation

```typescript
import * as SecureStore from 'expo-secure-store';

export async function saveUserSessionToken(token: string) {
  await SecureStore.setItemAsync('user_session_token', token, {
    keychainAccessible: SecureStore.AFTER_FIRST_UNLOCK,
  });
}

export async function getUserSessionToken(): Promise<string | null> {
  return await SecureStore.getItemAsync('user_session_token');
}
```

---

# 4. Expo `app.json` Sign in with Apple Entitlement

```json
{
  "expo": {
    "ios": {
      "usesAppleSignIn": true
    },
    "plugins": [
      "expo-apple-authentication"
    ]
  }
}
```

---

# 5. Operational Verification Checklist

- [ ] **Guideline 4.0 Compliant**: Sign in with Apple offered alongside any third-party social login options.
- [ ] **PKCE Enabled**: OAuth 2.0 flows use Authorization Code Grant with PKCE (`ASWebAuthenticationSession`).
- [ ] **Tokens Stored Securely**: Refresh and access tokens stored in iOS Keychain / Android Keystore (`expo-secure-store`).
- [ ] **No Secrets in JS**: OAuth client secrets are NEVER embedded in client app binaries.
- [ ] **Revocation Supported**: User can log out and revoke active tokens cleanly.

---

# 6. Related Documentation

- [Universal Links Guide](../universal-links/README.md) - Universal links.
- [Secret Protection](../../ai/security/secret-protection.md) - Secret protection rules.
- [Release Preparation Workflow](../../ai/workflows/release-preparation.md) - Pre-release checks.

---

# 7. Official Sources

- Apple Guideline 4.0 (Design - Sign in with Apple): https://developer.apple.com/app-store/review/guidelines/#design
- Sign in with Apple Overview: https://developer.apple.com/sign-in-with-apple/
- OAuth 2.0 for Native Apps (RFC 8252): https://datatracker.ietf.org/doc/html/rfc8252

---

**Last verified:** August 13, 2026

