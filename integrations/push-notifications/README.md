# Push Notifications Integration

This guide details the integration architecture, APNs authentication key (`.p8`) setup, Firebase Cloud Messaging (FCM) HTTP v1 API, Expo Notifications, OneSignal, notification channels, and release rules for **Push Notifications** in React Native and Expo applications.

It specifies how to configure remote notification payloads, handle device tokens, and manage background notification events while ensuring zero exposure of push server private keys.

This guide is **not**:

- an authorization mechanism to spam users with un-solicited marketing notifications
- a guide to deprecated FCM legacy HTTP APIs (migrated to FCM HTTP v1 API)
- a substitute for configuring push entitlements in `Info.plist` and `AndroidManifest.xml`

---

# 1. Integration Architecture & Delivery Pipeline

Push notification delivery relies on platform-specific push gateways (Apple Push Notification service - APNs; Firebase Cloud Messaging - FCM).

```text
[ Backend Push Trigger ]
           │
           ▼
┌────────────────────────────────────────────────────────┐
│             PUSH PROVIDER GATEWAY API                  │
│  - iOS: APNs HTTP/2 API with `.p8` Auth Key           │
│  - Android: FCM HTTP v1 API with Service Account JSON  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│                 NATIVE DEVICE HANDLER                  │
│  - iOS: `UNUserNotificationCenter`                     │
│  - Android: `NotificationManager` Channel Handler      │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Display System Notification / Trigger App Navigation ]
```

---

# 2. iOS APNs Credentials & Entitlements

To receive remote notifications on iOS:

1. **APNs Auth Key (`.p8`)**: Upload a valid APNs Auth Key (`.p8`) to Expo Application Services (EAS) Credentials or your push server. One `.p8` key works for all apps in your Apple Developer team.
2. **`aps-environment` Entitlement**: Ensure the push notification entitlement is declared:

```json
{
  "expo": {
    "ios": {
      "entitlements": {
        "aps-environment": "production"
      }
    },
    "plugins": [
      [
        "expo-notifications",
        {
          "icon": "./assets/notification-icon.png",
          "color": "#1E40AF"
        }
      ]
    ]
  }
}
```

---

# 3. Android FCM HTTP v1 & Notification Channels

Starting with Android 8.0 (API Level 26), all notifications MUST be assigned to a **Notification Channel**:

```typescript
import * as Notifications from 'expo-notifications';
import { Platform } from 'react-native';

export async function setupAndroidNotificationChannels() {
  if (Platform.OS === 'android') {
    await Notifications.setNotificationChannelAsync('default', {
      name: 'Default Notifications',
      importance: Notifications.AndroidImportance.HIGH,
      vibrationPattern: [0, 250, 250, 250],
      lightColor: '#1E40AF',
    });
  }
}
```

### FCM HTTP v1 Migration

Ensure server backend uses the modern **FCM HTTP v1 API** authenticated via OAuth 2.0 Service Account JSON rather than legacy FCM server keys.

---

# 4. In-Context Permission Request Timing

Do NOT trigger the native notification permission dialog on initial app launch. Prompt users in-context after they enable a feature requiring notifications (e.g., order tracking, messaging).

```typescript
export async function requestNotificationPermission() {
  const { status: existingStatus } = await Notifications.getPermissionsAsync();
  let finalStatus = existingStatus;

  if (existingStatus !== 'granted') {
    const { status } = await Notifications.requestPermissionsAsync();
    finalStatus = status;
  }

  return finalStatus === 'granted';
}
```

---

# 5. Operational Verification Checklist

- [ ] **APNs Key Configured**: Valid `.p8` key configured in EAS Credentials or push provider.
- [ ] **FCM HTTP v1 Active**: Backend push server uses FCM HTTP v1 OAuth 2.0 credentials.
- [ ] **Notification Channels Set**: Android notification channels configured for Android 8.0+.
- [ ] **Push Icon Assets Included**: Small monochromatic notification icon provided for Android.
- [ ] **In-Context Prompting**: Notification permission prompt is delayed until triggered by user feature action.

---

# 6. Related Documentation

- [Background Processing Guide](../background-processing/README.md) - Background processing tasks.
- [Secret Protection](../../ai/security/secret-protection.md) - Masking service account keys.
- [Release Preparation Workflow](../../ai/workflows/release-preparation.md) - Release prep checks.

---

# 7. Official Sources

- Apple Push Notification service (APNs): https://developer.apple.com/documentation/usernotifications
- Firebase Cloud Messaging HTTP v1 API: https://firebase.google.com/docs/cloud-messaging/migrate-v1

---

**Last verified:** August 13, 2026

