# Network Resilience & Proxy Error Testing

This document details network inspection proxy setups (Proxyman, Charles Proxy), network latency injection, HTTP status code error boundaries (401, 403, 429, 500, 503), and SSL pinning validation for **Network Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test mobile network resilience under adverse connection conditions.

This guide is **not**:

- an authorization mechanism to ignore HTTP 5xx backend server errors
- a substitute for testing slow 3G network profiles
- a guide to disabling SSL certificate validation in production builds

---

# 1. Network Proxy Inspection Setup (Proxyman / Charles)

Configure Proxyman or Charles Proxy to inspect, intercept, and rewrite HTTPS network traffic between the mobile application and backend API endpoints.

```text
┌────────────────────────────────────────────────────────┐
│             NETWORK PROXY TESTING ARCHITECTURE         │
│                                                        │
│  [ React Native Mobile App ]                           │
│              │ (HTTPS Fetch)                           │
│              ▼                                         │
│  [ Proxyman / Charles Proxy ] ──→ Inject Latency (2000ms)│
│              │                  ──→ Rewrite HTTP 500   │
│              ▼                                         │
│  [ Production Backend API ]                            │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Network Error Boundary Scenarios

| HTTP Status Code | Simulated Failure Scenario | Expected Application Behavior |
|---|---|---|
| **HTTP 401 Unauthorized** | Session token expired | Clear auth state; redirect to Login screen cleanly. |
| **HTTP 429 Rate Limited** | API rate limit exceeded | Display retry countdown notice; disable retry button. |
| **HTTP 500 / 503** | Internal server error | Display friendly error toast; allow manual pull-to-refresh. |
| **Timeout (Slow 3G)** | Network latency > 10,000ms | Display loading skeleton; trigger request timeout boundary. |

---

# 3. Operational Verification Checklist

- [ ] **HTTP 401 Session Handling**: Expired JWT token triggers clean redirect to Login screen.
- [ ] **HTTP 503 Error Toast**: Server error displays friendly error toast without app crash.
- [ ] **Slow 3G Resilience**: App tested on 3G profile; loading indicators display gracefully.
- [ ] **SSL Pinning Verified**: App rejects invalid SSL certificates when SSL pinning is active.

---

# 4. Official Sources

- Proxyman Documentation: https://docs.proxyman.io/
- Charles Proxy Documentation: https://www.charlesproxy.com/documentation/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Testing

- `testing/README.md`
- `testing/accessibility.md`
- `testing/android.md`
- `testing/deep-links.md`
- `testing/device-matrix.md`
- `testing/device-testing.md`
- `testing/e2e.md`
- `testing/integration.md`
- `testing/ios.md`
- `testing/localization.md`
- `testing/offline.md`
- `testing/payments.md`
- `testing/performance.md`
- `testing/push-notifications.md`
- `testing/release-smoke-tests.md`
- `testing/unit.md`
- `testing/upgrade-migrations.md`

### Pre-release

- `pre-release/README.md`
- `pre-release/final-release-checklist.md`

### Checklists

- `checklists/pre-release.md`

### Troubleshooting

- `troubleshooting/README.md`

### Release strategy

- `release-strategy/beta-testing.md`
