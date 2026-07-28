---
id: 202607282011
title: Dynamic Test Data Generation using Faker.js
aliases:
  - faker.js playwright
  - dynamic test data
  - test data generator
tags:
  - type/concept
  - status/processing
  - tool/playwright
  - topic/framework-design
date_created: 2026-07-28
mastery_level: 1
---

# Dynamic Test Data Generation using Faker.js

> **TL;DR:** Using `@faker-js/faker` inside test suites dynamically generates realistic user credentials, emails, addresses, and payment details on every run—preventing hardcoded test data collisions and duplicate registration failures!

---

## Canonical Code Example

```javascript
/**
 * Test Data Generator utility using @faker-js/faker
 */
import { faker } from '@faker-js/faker';

export class TestDataGenerator {
  static generateRandomUser() {
    return {
      firstName: faker.person.firstName(),
      lastName: faker.person.lastName(),
      email: faker.internet.email({ provider: 'testdomain.com' }),
      password: faker.internet.password({ length: 12, prefix: 'P@ss!' }),
      phone: faker.phone.number(),
      address: faker.location.streetAddress(),
      city: faker.location.city(),
      zipCode: faker.location.zipCode()
    };
  }
}
```

---

## Related
- [[pw-page-object-model-pattern]] — Using generated data in page objects.
- [[MOC - Playwright Framework Design]] — Framework design MOC.
