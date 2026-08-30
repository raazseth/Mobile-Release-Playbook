# Custom URL Scheme Deep Linking Integration

This guide details the integration architecture, scheme registration, React Navigation linking configuration, and security controls for **Custom URL Scheme Deep Linking** (`myapp://`) in React Native and Expo applications.

It specifies how to register custom URL schemes and route incoming deep link payloads while safeguarding against deep link parameter injection attacks.

This guide is **not**:

- an iOS Universal Links guide (see [universal-links/](../universal-links/README.md))
- an Android App Links guide (see [app-links/](../app-links/README.md))
- a recommendation to use custom schemes for sensitive token authentication

---

# 1. Integration Architecture & Scheme Registration

Custom URL schemes allow applications to respond to custom URI protocols (e.g., `myapp://product/123`).

```text
[ User Clicks Custom Scheme Link: `myapp://product/123` ]
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            OS CUSTOM SCHEME ROUTER                     │
│  - iOS: Checks `CFBundleURLTypes` in `Info.plist`      │
│  - Android: Checks `intent-filter` in `AndroidManifest`│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             REACT NAVIGATION LINKING ENGINE            │
│  - Parse URL scheme & path (`/product/123`)            │
│  - Validate path parameters & navigate to ProductScreen│
└────────────────────────────────────────────────────────┘
```

---

# 2. Scheme Registration in Expo & React Native

### Expo `app.json` Scheme Configuration

```json
{
  "expo": {
    "scheme": "myapp",
    "ios": {
      "bundleIdentifier": "com.example.mobileapp"
    },
    "android": {
      "package": "com.example.mobileapp"
    }
  }
}
```

### Generated `Info.plist` Excerpt (iOS)

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>myapp</string>
        </array>
    </dict>
</array>
```

---

# 3. React Navigation Linking Configuration

Configure the React Navigation `linking` object to map incoming URL paths to application screens:

```typescript
import { LinkingOptions } from '@react-navigation/native';
import * as Linking from 'expo-linking';

export const linking: LinkingOptions<RootStackParamList> = {
  prefixes: [Linking.createURL('/'), 'myapp://'],
  config: {
    screens: {
      Home: 'home',
      ProductDetails: 'product/:id',
      Settings: 'settings',
    },
  },
};
```

---

# 4. Deep Linking Security & Parameter Sanitization

Custom URL schemes can be triggered by any installed app on a user's device. Follow strict security practices:

1. **No Sensitive Tokens in Custom Schemes**: Never pass OAuth authorization codes, JWT access tokens, or session tokens in `myapp://` custom scheme URLs (other installed malicious apps can hijack custom schemes).
2. **Validate Path Parameters**: Sanitize and validate route parameters (`:id`) before executing backend queries or rendering UI.
3. **Use Domain Verification for Auth**: Use [Universal Links](../universal-links/README.md) or [App Links](../app-links/README.md) for authentication callbacks.

---

# 5. Operational Verification Checklist

- [ ] **Scheme Registered**: Scheme name (`myapp`) registered in `app.json` / `Info.plist` / `AndroidManifest.xml`.
- [ ] **React Navigation Mapped**: All deep-linkable screens configured in `linking.config.screens`.
- [ ] **No Secrets in URLs**: Custom schemes free of sensitive tokens or credentials.
- [ ] **Parameter Validation**: Input parameters sanitized before state updates.
- [ ] **CLI Test Passed**: Deep link opens app correctly when tested via CLI (`npx uri-scheme open myapp://product/123 --ios`).

---

# 6. Related Documentation

- [Universal Links Integration Guide](../universal-links/README.md) - iOS Universal Links.
- [App Links Integration Guide](../app-links/README.md) - Android App Links.
- [OAuth Integration Guide](../oauth/README.md) - Authentication flows.

---

# 7. Official Sources

- Expo Deep Linking Documentation: https://docs.expo.dev/guides/deep-linking/
- React Navigation Linking: https://reactnavigation.org/docs/deep-linking/

---

**Last verified:** August 13, 2026

