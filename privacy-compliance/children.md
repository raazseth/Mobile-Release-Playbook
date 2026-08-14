# COPPA & Kids Category Compliance Handbook

This handbook details Children's Online Privacy Protection Act (COPPA) requirements, Apple Kids Category rules, Google Play Designed for Families policy, neutral age gates, and tracking bans for **Children's Applications** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to publish compliant applications targeted at children under 13.

This guide is **not**:

- an authorization mechanism to embed third-party advertising SDKs in kids apps
- a substitute for implementing neutral age gates
- a guide to collecting personal information from children

---

# 1. Kids Category Compliance Architecture

Apps in Apple's Kids Category or Google Play's Families Program must adhere to strict restrictions:

```text
┌────────────────────────────────────────────────────────┐
│             KIDS CATEGORY STRICT RESTRICTIONS          │
│                                                        │
│  - ZERO Third-Party Behavioral Advertising / IDFA      │
│  - ZERO Un-Moderated User-Generated Content (UGC)      │
│  - ZERO External Links without Parental Gate           │
│  - Neutral Age Gate for Age Verification               │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Compliant with COPPA & Store Families Policies ]
```

---

# 2. Neutral Age Gate Implementation

A neutral age gate asks users to enter their date of birth without suggesting the correct age:

```tsx
import React, { useState } from 'react';
import { View, Text, Button, TextInput } from 'react-native';

export function NeutralAgeGate({ onAgeVerified }: { onAgeVerified: (isAdult: boolean) => void }) {
  const [birthYear, setBirthYear] = useState('');

  const handleVerify = () => {
    const currentYear = new Date().getFullYear();
    const age = currentYear - parseInt(birthYear, 10);
    onAgeVerified(age >= 13);
  };

  return (
    <View>
      <Text>Please enter your birth year to continue:</Text>
      <TextInput
        keyboardType="number-pad"
        value={birthYear}
        onChangeText={setBirthYear}
        placeholder="YYYY"
      />
      <Button title="Continue" onPress={handleVerify} />
    </View>
  );
}
```

---

# 3. Operational Verification Checklist

- [ ] **Zero Behavioral Ad SDKs**: No third-party ad networks or IDFA tracking integrated.
- [ ] **Parental Gate Active**: External web links and in-app purchases protected by parental math gate.
- [ ] **Neutral Age Gate Implemented**: Age gate prompts for birth year without indicating target threshold.

---

# 4. Official Sources

- FTC COPPA Guidance: https://www.ftc.gov/legal-library/browse/rules/childrens-online-privacy-protection-rule-coppa
- Apple Kids Category Guidelines: https://developer.apple.com/app-store/kids-apps/

---

**Last verified:** August 14, 2026
