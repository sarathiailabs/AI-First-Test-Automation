# Day 14: Fixtures + Hooks + Test Data – Assignments

This assignment file contains theoretical questions and practical programming tasks designed to reinforce Playwright fixtures, setup/teardown hooks, storage authentication state sharing, external JSON test data inputs, environment variables loading, and configuration files.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is a Playwright **Fixture**? How does it differ from a **beforeEach** hook in test execution?

### Question 2 🔥 **Frequently Asked**
What is the purpose of **Shared Authentication** (Storage State)? Describe how to save browser state details to a JSON file and load them globally.

### Question 3 📌 **Important**
What is **Test Data Parameterization**? How does loading credentials from a JSON file help reduce code redundancy in authentication scripts?

### Question 4 📌 **Important**
Explain the differences between global timeout limits (`timeout`) and assertion timeout limits (`expect.timeout`) inside the `playwright.config.ts` configuration file.

### Question 5 💡 **Good to Know**
Why should test scripts treat imported JSON data files as read-only? What issue can happen if Test 1 updates a value in a shared JSON object at runtime?

---

## Practical Assignments

### Assignment 1: Custom Login Page Fixture

* **Interview Relevance:** Designing custom page fixtures demonstrates advanced proficiency in framework design, replacing boilerplate Page Object declarations inside spec files.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `test.extend()`, `yield`, Custom fixtures definitions, POM instantiation.

#### Problem Statement
Write a custom fixture file named `vjti-fixtures.ts` that defines a fixture `loginPage`. The fixture must instantiate the `VJTI_LoginPage` class, call its `navigate()` method, and pass the object to the test.

**LoginPage class setup (for imports):**
```typescript
import { Page } from '@playwright/test';
export class VJTI_LoginPage {
  readonly page: Page;
  constructor(page: Page) { this.page = page; }
  async navigate() { await this.page.goto('https://api.vjti.edu/login'); }
  async login(user: string, pin: string) { /* fill inputs */ }
}
```

#### Requirements
1. Create `vjti-fixtures.ts`.
2. Extend the base test using `base.extend<MyFixtures>`.
3. Implement the `loginPage` fixture:
   - Instantiate `VJTI_LoginPage`.
   - Call `await loginPage.navigate()`.
   - Yield the instance using `await use(loginPage)`.
   - Print a console log during the teardown phase.
4. Export the customized `test` and `expect`.

#### Expected Output
```text
  ✓ Setup: LoginPage initialized and navigated
  ✓ Teardown: LoginPage cleanup complete
```

---

### Assignment 2: Parameterized Login Verification with JSON

* **Interview Relevance:** Data-driven testing is a core framework requirement. Parameterization demonstrates ability to execute a test template across variable input matrices.
* **Difficulty Level:** Easy-Intermediate
* **Concepts Covered:** JSON data import, `for (const item of data)` loops, parameterized test naming.

#### Problem Statement
Write a Playwright script named `data-driven-login.spec.ts` that loops over a JSON file containing student credentials and validates login attempts.

**External Input File (`student-credentials.json`):**
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

#### Requirements
1. Create `student-credentials.json` with the structure above.
2. In `data-driven-login.spec.ts`, import the JSON data.
3. Write a loop to iterate over each student record.
4. Define a test for each record:
   - Navigate to the login page.
   - Fill in `regNo` and `pin`.
   - Click login.
   - Assert that the error alert displays `expectedError`.

#### Expected Output
```text
  ✓ Verify login validation for vjti-2026-101 (Pass)
  ✓ Verify login validation for vjti-2026-102 (Pass)
```

---

### Assignment 3: Dynamic Environment URL Configuration

* **Interview Relevance:** Decoupling host URLs from tests to support QA, staging, and production environments is a standard pipeline setup.
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** Environment variable mapping, configuration files project variables, dynamic baseURL loading.

#### Problem Statement
Configure the Playwright configuration file to load the target test environment's base URL dynamically at runtime using environment variables.

#### Requirements
1. Modify `playwright.config.ts` configuration.
2. Use `process.env.TEST_ENV` to determine the target environment (e.g. `'staging'` or `'prod'`).
3. Set `baseURL` inside the config file:
   - If `TEST_ENV` is `'staging'`, set `baseURL: 'https://staging.vjti.edu'`.
   - If `TEST_ENV` is `'prod'`, set `baseURL: 'https://api.vjti.edu'`.
   - Default to `'https://localhost:3000'` if `TEST_ENV` is not set.
4. Export the configuration.

#### Hints
* Read environment variables using standard NodeJS `process.env.TEST_ENV`.
