# Push Notification Delivery Failures Triage

This document details diagnostic workflows, invalid APNs `.p8` token authentication errors, FCM HTTP v1 migration failures, missing background notification handlers, and device token registration errors for **Push Notification Troubleshooting** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic steps for resolving push notification delivery breakdowns.

This guide is **not**:

- an authorization mechanism to use deprecated FCM legacy HTTP APIs
- a substitute for verifying APNs push credentials in Apple Developer Portal
- a guide to ignoring background payload handler exceptions

---

# 1. Push Notification Diagnostic Flow

```text
┌────────────────────────────────────────────────────────┐
│             PUSH NOTIFICATION DIAGNOSTIC FLOW          │
│                                                        │
│  - Test token generation on physical device            │
│  - Inspect backend notification delivery log           │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
[ APNs Auth Error (400 BadDeviceToken) ] [ FCM HTTP v1 `UNREGISTERED` ]
Regenerate APNs `.p8` key in        Device token expired or app
Apple Developer Portal              uninstalled; clean token DB
```

---

# 2. Key Push Notification Errors & Resolutions

### Scenario A: APNs `400 BadDeviceToken`
- **Root Cause**: Device token generated for Sandbox environment submitted to Production APNs gateway (or vice versa).
- **Resolution**: Ensure push token registration environment matches target APNs endpoint (`api.development.push.apple.com` vs `api.push.apple.com`).

### Scenario B: FCM Legacy HTTP API `401 Unauthorized`
- **Root Cause**: Backend uses deprecated FCM Server Key URL (`fcm.googleapis.com/fcm/send`).
- **Resolution**: Migrate backend push service to FCM HTTP v1 API (`fcm.googleapis.com/v1/projects/{project-id}/messages:send`) using OAuth2 service account tokens.

---

# 3. Operational Verification Checklist

- [ ] **FCM HTTP v1 Active**: Backend uses FCM HTTP v1 API with service account OAuth2 tokens.
- [ ] **APNs `.p8` Key Valid**: APNs Key ID and Team ID verified in App Store Connect.
- [ ] **Token Environment Matched**: Development vs Production push tokens directed to matching gateways.

---

# 4. Official Sources

- FCM Migration to HTTP v1: https://firebase.google.com/docs/cloud-messaging/migrate-v1

---

**Last verified:** August 14, 2026
