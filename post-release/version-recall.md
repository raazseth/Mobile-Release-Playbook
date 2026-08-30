# Critical Version Removal & Forced Upgrades Handbook

This handbook details App Store version removal ("Remove from Sale"), forced app update UI modals (`minSupportedVersion`), API deprecation headers, and backend version gates for **Version Recall** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to force users off un-safe legacy app builds.

This guide is **not**:

- an authorization mechanism to break legacy app versions without user notice
- a substitute for implementing server-driven forced update UI gates
- a guide to removing an app from sale without providing a patch update

---

# 1. Server-Driven Forced Upgrade Architecture

When a released app version contains a severe security flaw or broken API integration, a server-driven forced update modal blocks usage until the user updates to the latest app store version.

```text
┌────────────────────────────────────────────────────────┐
│             FORCED UPGRADE UI ARCHITECTURE             │
│                                                        │
│  - App Launch ──→ Query Backend `/api/v1/config/version`│
│  - Server Returns: `{ "minSupportedVersion": "1.2.1" }`│
│  - Client Compares: Local Version (1.1.0) < 1.2.1      │
│  - Render Non-Dismissible Forced Update Modal UI       │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ 'Update Now' Button Opens App Store / Play Store Product Page ]
```

---

# 2. Forced Update Modal Implementation Pattern

```tsx
import React from 'react';
import { Modal, View, Text, Button, Linking, Platform } from 'react-native';

export function ForcedUpdateModal({ visible, storeUrl }: { visible: boolean; storeUrl: string }) {
  return (
    <Modal visible={visible} transparent={false} animationType="fade">
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center', padding: 20 }}>
        <Text style={{ fontSize: 22, fontWeight: 'bold', marginBottom: 12 }}>Update Required</Text>
        <Text style={{ textAlign: 'center', marginBottom: 20 }}>
          A critical update is required to continue using this application. Please update to the latest version.
        </Text>
        <Button title="Update Now" onPress={() => Linking.openURL(storeUrl)} />
      </View>
    </Modal>
  );
}
```

---

# 3. Operational Verification Checklist

- [ ] **`minSupportedVersion` Configured**: Backend API endpoint returns minimum required version string.
- [ ] **Modal Non-Dismissible**: Forced update modal cannot be dismissed or bypassed by user.
- [ ] **Store Links Functional**: "Update Now" button redirects directly to App Store / Play Store product page.

---

# 4. Official Sources

- Apple Removing an App from Sale: https://developer.apple.com/help/app-store-connect/#/devf8cf89679

---

**Last verified:** August 14, 2026

---

# Related documentation

### Post-release

- `post-release/README.md`
- `post-release/analytics.md`
- `post-release/crash-analysis.md`
- `post-release/hotfix.md`
- `post-release/incident-response.md`
- `post-release/monitoring.md`
- `post-release/performance.md`
- `post-release/release-retrospective.md`
- `post-release/reviews-and-ratings.md`
- `post-release/rollback.md`
- `post-release/rollout-monitoring.md`

### Release strategy

- `release-strategy/staged-rollouts.md`
- `release-strategy/kill-switches.md`

### Troubleshooting

- `troubleshooting/crash-after-release.md`

### Pre-release

- `pre-release/release-readiness.md`

### AI workflows

- `ai/workflows/debugging.md`
- `ai/agents/debugging-agent.md`

### Templates

- `templates/incident-report.md`
