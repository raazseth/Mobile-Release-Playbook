# User Account & Data Deletion Requirements Handbook

This handbook details the mandatory in-app account deletion buttons, public web-based deletion URLs, backend data purging SLAs, and store policy compliance for **Data Deletion** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to implement account and data deletion mechanisms mandated by Apple and Google Play.

This guide is **not**:

- an authorization mechanism to retain user data indefinitely after account deletion
- a substitute for providing a public web-based deletion link
- a guide to obfuscating account deletion options inside complex menu sub-trees

---

# 1. Dual Channel Data Deletion Architecture

Store platforms enforce two mandatory channels for requesting account and data deletion:

```text
                                DUAL DATA DELETION CHANNELS
                                             │
       ┌─────────────────────────────────────┴─────────────────────────────────────┐
       ▼                                                                           ▼
[ IN-APP DELETION BUTTON ]                                         [ WEB-BASED DELETION URL ]
- Accessible inside App Settings menu                              - Public `https://` web link
- Confirms user identity & triggers backend purge                   - Accessible without installing app
- Implemented via React Native UI flow                             - Required by Google Play Console
```

---

# 2. In-App Deletion Implementation Pattern

```tsx
import React, { useState } from 'react';
import { Alert, TouchableOpacity, Text } from 'react-native';

export function AccountDeletionButton() {
  const [isDeleting, setIsDeleting] = useState(false);

  const handleDeleteAccount = () => {
    Alert.alert(
      'Delete Account & Data',
      'Are you sure you want to permanently delete your account? All saved workouts and data will be erased immediately. This action cannot be undone.',
      [
        { text: 'Cancel', style: 'cancel' },
        {
          text: 'Delete Permanently',
          style: 'destructive',
          onPress: async () => {
            setIsDeleting(true);
            await api.deleteUserAccount();
            // Clear local storage and redirect to auth
            await clearLocalSession();
          },
        },
      ]
    );
  };

  return (
    <TouchableOpacity onPress={handleDeleteAccount} disabled={isDeleting}>
      <Text style={{ color: 'red' }}>Delete Account & All Personal Data</Text>
    </TouchableOpacity>
  );
}
```

---

# 3. Operational Verification Checklist

- [ ] **In-App Deletion Active**: "Delete Account" button accessible inside app settings.
- [ ] **Web Deletion URL Live**: Public web URL (`https://example.com/delete-account`) configured in Play Console.
- [ ] **Backend Purge Executed**: Account deletion permanently purges personal data from backend databases within 30 days.

---

# 4. Official Sources

- Apple Account Deletion Requirement: https://developer.apple.com/support/offering-account-deletion-in-your-app/
- Google Play Account Deletion Requirement: https://support.google.com/googleplay/android-developer/answer/13327111

---

**Last verified:** August 14, 2026

---

# Related documentation

### Privacy compliance

- `privacy-compliance/README.md`
- `privacy-compliance/advertising.md`
- `privacy-compliance/analytics.md`
- `privacy-compliance/children.md`
- `privacy-compliance/data-collection.md`
- `privacy-compliance/data-retention.md`
- `privacy-compliance/health-data.md`
- `privacy-compliance/permissions.md`
- `privacy-compliance/privacy-policy.md`
- `privacy-compliance/regional-requirements.md`
- `privacy-compliance/tracking.md`

### Store operations

- `store-operations/data-safety.md`
- `store-operations/privacy-information.md`

### Pre-release

- `pre-release/privacy-audit.md`

### AI agents

- `ai/agents/privacy-auditor.md`

### Monetization

- `monetization/receipt-validation.md`

### Troubleshooting

- `troubleshooting/privacy-rejected.md`
