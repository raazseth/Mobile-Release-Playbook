# Push Notification Testing & Payload Handlers

This document details push notification testing protocols, FCM HTTP v1 payload verification, APNs `.p8` token testing, background payload handlers, and Expo Push Service integration for **Push Notification Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test notification delivery, permission prompts, and background data payloads.

This guide is **not**:

- an authorization mechanism to test push notifications without valid APNs/FCM tokens
- a substitute for testing background payload handlers
- a guide to using legacy FCM HTTP APIs (FCM HTTP v1 is mandatory)

---

# 1. Push Notification Architecture & Payload Testing

Push notifications travel from backend servers through Apple Push Notification service (APNs) or Firebase Cloud Messaging (FCM HTTP v1) to end-user devices.

```text
┌────────────────────────────────────────────────────────┐
│             PUSH NOTIFICATION PAYLOAD TEST             │
│                                                        │
│  - Foreground: `Notifications.addNotificationReceived`  │
│  - Background / Tap: `Notifications.addNotificationResponseReceived`│
│  - Data Payload: `{ "screen": "workout", "id": "123" }`│
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ App in Foreground ]               [ App in Background / Closed ]
Custom in-app banner renders        System notification banner renders;
without system banner               tapping opens app to target screen
```

---

# 2. Simulator Push Notification Payload Injection (`.apns`)

Test iOS push notifications on iOS Simulators by dragging a `.apns` file onto the simulator viewport:

```json
{
  "Simulator Target Bundle": "com.example.mobileapp",
  "aps": {
    "alert": {
      "title": "New Workout Assigned",
      "body": "Your trainer assigned a new 30-minute HIIT workout."
    },
    "badge": 1,
    "sound": "default"
  },
  "screen": "workout",
  "workoutId": "123"
}
```

---

# 3. Operational Verification Checklist

- [ ] **FCM HTTP v1 Payload Active**: Firebase payloads comply with FCM HTTP v1 protocol.
- [ ] **APNs `.p8` Key Validated**: APNs key authenticated cleanly with Apple servers.
- [ ] **Foreground Handler Renders**: In-app banner or toast renders when app is open.
- [ ] **Background Tap Navigates**: Tapping background notification banner opens target screen.
- [ ] **Permission Prompt Tested**: Push notification permission dialog tested on fresh install.

---

# 4. Official Sources

- Expo Notifications Guide: https://docs.expo.dev/versions/latest/sdk/notifications/
- Firebase Cloud Messaging FCM HTTP v1: https://firebase.google.com/docs/cloud-messaging/migrate-v1

---

**Last verified:** August 14, 2026
