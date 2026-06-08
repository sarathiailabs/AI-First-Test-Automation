# Day 14: Fixtures + Hooks + Test Data – Solutions

This file contains solutions for the theory questions and practical programming assignments assigned for Day 14.

---

## Theory Question Solutions

### Question 1
What is a Playwright **Fixture**? How does it differ from a **beforeEach** hook in test execution?

### Answer
* **Playwright Fixture:** A modular, pre-configured object passed into a test argument, containing both setup and teardown instructions.
* **Key Differences:**
  * **Encapsulation:** Fixtures encapsulate setup and teardown together using the `yield` keyword. Hooks require separate blocks (`beforeEach` and `afterEach`).
  * **On-Demand:** Fixtures run only if a test explicitly lists them as parameters. Hooks run globally for every test inside the file, even if a test doesn't need them.
  * **Execution Scope:** Fixtures can share states easily and chain together (e.g. an authenticated page fixture uses a base page fixture).

---

### Question 2
What is the purpose of **Shared Authentication** (Storage State)? Describe how to save browser state details to a JSON file and load them globally.

### Answer
* **Purpose:** To log in once, extract cookies and local storage tokens, and inject them into subsequent test page sessions to bypass login pages and save execution time.
* **Saving State:** Run a setup script and capture state:
  ```typescript
  await page.context().storageState({ path: 'auth.json' });
  ```
* **Loading State:** Configure the saved file path in `playwright.config.ts`:
  ```typescript
  use: { storageState: 'auth.json' }
  ```

---

### Question 3
What is **Test Data Parameterization**? How does loading credentials from a JSON file help reduce code redundancy in authentication scripts?

### Answer
* **Parameterization:** Executing the same test logic template across a list of different input data rows.
* **Redundancy Reduction:** Instead of writing 10 separate test blocks to verify that 10 different invalid login entries trigger errors, you write a single loop that imports a JSON file containing the inputs and runs the test template dynamically, keeping code clean and short.

---

### Question 4
Explain the differences between global timeout limits (`timeout`) and assertion timeout limits (`expect.timeout`) inside the `playwright.config.ts` configuration file.

### Answer
* **Global Timeout (`timeout`):** The absolute maximum duration allowed for a single test block (including all hooks, setups, steps, and teardowns) before Playwright aborts it (default is 30 seconds).
* **Assertion Timeout (`expect.timeout`):** The maximum duration an assertion waits (e.g. waiting for an element to become visible) before failing the expectation step (default is 5 seconds).

---

### Question 5
Why should test scripts treat imported JSON data files as read-only? What issue can happen if Test 1 updates a value in a shared JSON object at runtime?

### Answer
* **Why Read-Only:** JavaScript imports JSON files as objects by reference. If Test 1 modifies a property in the imported object, that change persists in memory.
* **What happens:** If Test 1 changes a username to `'modified'`, and Test 2 runs in the same thread expecting the original username from the JSON, Test 2 will fail because the shared object in memory was mutated, causing flaky and dependent test bugs.

---

## Programming Assignment Solutions

### Assignment 1: Custom Login Page Fixture

#### Solution Code (`vjti-fixtures.ts`)
```typescript
import { test as base } from '@playwright/test';
import { VJTI_LoginPage } from './VJTI_LoginPage';

// 1. Define custom fixture types
type MyFixtures = {
  loginPage: VJTI_LoginPage;
};

// 2. Extend base test to define loginPage fixture
export const test = base.extend<MyFixtures>({
  loginPage: async ({ page }, use) => {
    console.log('Setup: Initializing LoginPage POM...');
    const loginPage = new VJTI_LoginPage(page);
    await loginPage.navigate(); // Pre-navigate to login url

    await use(loginPage); // Yield control to the test script

    // Teardown phase runs after test completion
    console.log('Teardown: Cleaning LoginPage session context...');
  }
});

export { expect } from '@playwright/test';
```

---

### Assignment 2: Parameterized Login Verification with JSON

#### Solution Code

##### JSON Data file (`student-credentials.json`)
```json
[
  {
    "regNo": "vjti-2026-101",
    "pin": "1111",
    "expectedError": "Invalid Credentials"
  },
  {
    "regNo": "vjti-2026-102",
    "pin": "2222",
    "expectedError": "Invalid Credentials"
  }
]
```

##### Test Script (`data-driven-login.spec.ts`)
```typescript
import { test, expect } from '@playwright/test';
import studentsData from './student-credentials.json';

// Parameterized loop
for (const student of studentsData) {
  test(`Verify login validation for ${student.regNo}`, async ({ page }) => {
    await page.goto('https://api.vjti.edu/login');
    
    // Fill credentials
    await page.locator('#username').fill(student.regNo);
    await page.locator('#student-pin').fill(student.pin);
    await page.locator('#login-submit').click();

    // Assert error alert displays expected message
    const errorAlert = page.locator('.error-alert-message');
    await expect(errorAlert).toHaveText(student.expectedError);
  });
}
```

---

### Assignment 3: Dynamic Environment URL Configuration

#### Solution Code (`playwright.config.ts`)
```typescript
import { defineConfig, devices } from '@playwright/test';

// 1. Read environmental target variable
const environment = process.env.TEST_ENV || 'local';

// 2. Map environments to base URLs
let targetBaseUrl = 'https://localhost:3000';

if (environment === 'staging') {
  targetBaseUrl = 'https://staging.vjti.edu';
} else if (environment === 'prod') {
  targetBaseUrl = 'https://api.vjti.edu';
}

console.log(`Config: Environment target set to [${environment}] pointing to ${targetBaseUrl}`);

// 3. Export configuration
export default defineConfig({
  testDir: './tests',
  timeout: 30000,
  use: {
    baseURL: targetBaseUrl, // Dynamically loaded base URL
    screenshot: 'only-on-failure',
    trace: 'retain-on-failure'
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    }
  ]
});
```
