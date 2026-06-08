# Day 14: Fixtures + Hooks + Test Data

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Fixtures | 20 mins |
| Hooks | 20 mins |
| Shared Authentication | 20 mins |
| Test Data Management | 20 mins |
| Environment Management | 20 mins |
| Configuration Files | 20 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Build and use custom Playwright **Fixtures** to set up page object state automatically.
* Structure test sequences cleanly using lifecycle **Hooks** (`beforeEach`, `afterAll`, etc.).
* Configure **Shared Authentication** to skip login screens across test suites.
* Decouple inputs from test code using JSON files for **Test Data Management**.
* Switch test configurations dynamically between Staging and Production for **Environment Management**.
* Configure global options (retries, timeouts, browsers) inside Playwright **Configuration Files**.

---

## Fixtures

### Definition
**Fixtures** are pre-configured environments or objects prepared before running a test, automatically passed as arguments, and cleaned up cleanly after the test finishes. *(Fixtures ready-to-use helpers hote hain jo test run hone se pehle setup ho jaate hain aur test khatam hone par clean up ho jaate hain).*

### Key Concepts
* **Built-in Fixtures:** Playwright provides default fixtures like `page`, `request`, and `context`.
* **Custom Fixtures:** You can define your own fixtures to instantiate and navigate Page Object classes automatically, eliminating repetitive setup code in spec files.
* **Teardown encapsulation:** Fixtures use the `yield` keyword. Code before `yield` is the setup phase; code after `yield` runs during the teardown phase.

### Visual Explanation
**The Rented Event Hall Analogy:**
Imagine organizing a seminar at VJTI.
* **Without Fixtures:** You arrive early, sweep the floor, arrange chairs, and set up the projector. After the session, you clean up, fold chairs, and lock doors. You repeat this for *every* seminar.
* **With Fixtures:** You rent a pre-configured seminar hall. The manager handles setup and teardown. You just walk in, deliver the talk, and walk out. A fixture acts as this automated hall manager.

### Example
#### Custom Fixture Definition (`my-fixtures.ts`)
```typescript
import { test as base } from '@playwright/test';
import { LoginPage } from './LoginPage';

// Define the custom fixture types
type MyFixtures = {
  loginPage: LoginPage;
};

// Extend the base test configuration to include the custom fixture
export const test = base.extend<MyFixtures>({
  loginPage: async ({ page }, use) => {
    const loginPage = new LoginPage(page);
    await loginPage.navigate(); // Setup phase
    
    await use(loginPage); // Pass the fixture object to the test script
    
    // Teardown phase (runs automatically after the test finishes)
    console.log('Teardown: Cleaning session...');
  }
});

export { expect } from '@playwright/test';
```
#### Usage inside test script (`portal.spec.ts`)
```typescript
import { test, expect } from './my-fixtures'; // Import custom test loader

test('Verify dashboard login via custom fixture', async ({ loginPage }) => {
  // The loginPage fixture is ready to use and already navigated to the page
  await loginPage.login('student1', 'pass123');
  // Run verification assertions
});
```

### Topic Summary
Custom fixtures extend standard Playwright runner suites to encapsulate POM setup and teardown tasks via the `yield` / `use` pattern.

---

## Hooks

### Definition
**Hooks** are lifecycle methods provided by testing frameworks that execute automatically at designated checkpoints before or after test runs to manage setup and cleanup logic. *(Hooks lifecycle checkpoints hain jo tests se pehle ya baad me setup-cleanup scripts run karne ke kaam aate hain).*

### Key Concepts
* **beforeAll:** Executes once before the entire test suite starts (e.g. seeding a database).
* **beforeEach:** Executes before *every* individual test block (e.g., loading a page url).
* **afterEach:** Executes after *every* individual test block (e.g., taking screenshots on failure).
* **afterAll:** Executes once after all tests in the file complete (e.g., closing database connections).

### Visual Explanation
```text
  [ beforeAll ] (Runs 1 time)
       │
       ├──► [ beforeEach ] ──► [ TEST 1 ] ──► [ afterEach ]
       │
       ├──► [ beforeEach ] ──► [ TEST 2 ] ──► [ afterEach ]
       │
  [ afterAll ] (Runs 1 time)
```

### Example
#### Code
```typescript
import { test, expect } from '@playwright/test';

test.beforeAll(async () => {
  console.log('Connecting to VJTI Database...');
});

test.beforeEach(async ({ page }) => {
  await page.goto('https://api.vjti.edu/portal');
});

test('Check Academics Title', async ({ page }) => {
  await expect(page.locator('.records-header')).toBeVisible();
});

test.afterAll(async () => {
  console.log('Disconnecting database connections...');
});
```

### Topic Summary
Hooks orchestrate test runner checkpoints, managing resource cleanups and setup states.

---

## Shared Authentication

### Definition
**Shared Authentication** is an optimization strategy where the browser's login state (cookies, local storage, tokens) is captured once, saved to a state file, and automatically injected into browser contexts for all subsequent tests, bypassing the login page. *(Shared Authentication login cookies ko ek file me save karta hai taaki aage ke tests bina credentials fill kiye direct logged-in page par launch ho skein).*

### Key Concepts
* **Storage State:** Playwright reads/writes auth states using `browserContext.storageState()`.
* **Saves Time:** Logging in through UI forms takes 3-5 seconds. Saving storage state reduces login overhead to milliseconds across a large suite.
* **Config injection:** Path configuration is set in the `playwright.config.ts` file.

### Visual Explanation
**The Amusement Park Wristband Analogy:**
When you enter a theme park:
* **Without Shared Auth:** Every time you go on a ride, you stand in a ticket line, show your ID, pay cash, and get verified.
* **With Shared Auth:** You pay once at the gate and get a wristband (**Storage State File**). Now, you just show the wristband at every ride to get instant entry.

### Example
#### Auth Setup Test (`auth.setup.ts`)
```typescript
import { test as setup } from '@playwright/test';

setup('Authenticate student session', async ({ page }) => {
  await page.goto('https://api.vjti.edu/login');
  await page.locator('#username').fill('vjti-admin');
  await page.locator('#student-pin').fill('9876');
  await page.locator('#login-submit').click();

  // Wait for login success indicators
  await page.waitForURL(/.*dashboard/);

  // Save auth cookies & storage parameters to a JSON file
  await page.context().storageState({ path: 'playwright/.auth/user.json' });
});
```
#### Configure Storage State injection (`playwright.config.ts`)
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  use: {
    // Automatically inject active authentication tokens to all browser sessions
    storageState: 'playwright/.auth/user.json',
  },
});
```

### Topic Summary
Shared authentication bypasses UI login flows by writing active browser storage parameters to state files loaded dynamically in configs.

---

## Test Data Management

### Definition
**Test Data Management** is the design pattern of separating hardcoded input values (like usernames, payment amounts, search keys) from test scripts, storing them in external files (like JSON), and importing them dynamically. *(Test Data Management ka matlab hai usernames aur passwords jaise inputs ko test script se alag JSON files me store karna).*

### Key Concepts
* **Data Parameterization:** Running the same test logic multiple times with different data inputs (e.g. testing registration with 5 different student names).
* **JSON format:** Key-value format that NodeJS parses natively.

### Example
#### Data File (`student-data.json`)
```json
[
  {
    "username": "student_amit",
    "pin": "1234",
    "expectedName": "Amit Sharma"
  },
  {
    "username": "student_rahul",
    "pin": "5678",
    "expectedName": "Rahul Verma"
  }
]
```
#### Test Script (`portal-data.spec.ts`)
```typescript
import { test, expect } from '@playwright/test';
import students from './student-data.json'; // Import JSON array directly

// Loop through each student record to run parameterized tests
for (const student of students) {
  test(`Verify login profile for ${student.username}`, async ({ page }) => {
    await page.goto('https://api.vjti.edu/login');
    await page.locator('#username').fill(student.username);
    await page.locator('#student-pin').fill(student.pin);
    await page.locator('#login-submit').click();

    await expect(page.locator('.profile-name')).toHaveText(student.expectedName);
  });
}
```

### Common Mistakes
* **Modifying shared data at runtime:** Allowing tests to modify properties inside imported JSON files. Since Javascript imports objects by reference, modifying values in Test 1 can corrupt inputs for Test 2, causing test dependency bugs. Treat test data as read-only.

### Topic Summary
JSON file integrations decouple test inputs from scripts, enabling parameterized iteration loops.

---

## Environment Management

### Definition
**Environment Management** is the system configuration pattern that allows the automation suite to dynamically switch its base URL, logins, and API ports at runtime based on the target environment (e.g., Local, QA Staging, or Production) using external settings. *(Environment Management test execution targets ko local machine se Staging ya Prod environment me swap karne ka execution method hai).*

### Key Concepts
* **Decoupled Endpoints:** Avoid hardcoding `localhost:3000` or `vjti-staging.edu` in code.
* **Environment files:** Use files like `.env.staging` or `.env.production` alongside package runners to specify hosts.

### Example
#### Env file (`.env.staging`)
```text
BASE_URL=https://staging.vjti.edu
ADMIN_USER=admin-stage
```
#### Code Implementation
```typescript
import { test, expect } from '@playwright/test';
import * as dotenv from 'dotenv';
import * as path from 'path';

// Load env configuration dynamically
const envName = process.env.ENV_TARGET || 'staging';
dotenv.config({ path: path.resolve(__dirname, `../../.env.${envName}`) });

test('Navigate dynamically based on env', async ({ page }) => {
  const targetUrl = process.env.BASE_URL; // e.g. https://staging.vjti.edu
  await page.goto(targetUrl!);
  // Run assertions
});
```

### Topic Summary
Environment management resolves targeted application servers dynamically at runtime using configurations decoupled from code.

---

## Configuration Files

### Definition
**Configuration Files** (specifically `playwright.config.ts`) are central settings files containing framework-wide instructions that control test timeouts, retries, browser types, reporting formats, parallel execution threads, and setup dependencies. *(Configuration files framework ke rules settings set karte hain jaise timeouts, browser options, aur parallel run settings).*

### Key Concepts
* **Timeouts:** Global timeout limits (e.g., maximum test length 30s, expectation wait 5s).
* **Parallel Execution:** Running multiple tests concurrently to save pipeline duration.
* **Projects:** Structuring browser runs (Chromium, Firefox, WebKit) and dependencies (running `auth.setup.ts` before executing tests).

### Example
#### Playwright Configuration (`playwright.config.ts`)
```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests',
  timeout: 30000, // Maximum time one test can run (30 seconds)
  expect: {
    timeout: 5000, // Timeout for expect assertions (5 seconds)
  },
  fullyParallel: true, // Run tests in parallel files
  retries: 1, // Retry failed tests once in CI pipelines
  reporter: 'html', // Generate readable HTML reports

  use: {
    baseURL: 'https://api.vjti.edu',
    trace: 'on-first-retry', // Record traces on failed attempts
    screenshot: 'only-on-failure', // Record screenshots on failure
  },

  projects: [
    // Project dependency: setup authentication first
    {
      name: 'setup',
      testMatch: /.*auth\.setup\.ts/,
    },
    {
      name: 'chromium',
      use: { 
        ...devices['Desktop Chrome'],
        // Inject auth cookies saved during setup project
        storageState: 'playwright/.auth/user.json', 
      },
      dependencies: ['setup'], // Run setup first
    }
  ],
});
```

### Topic Summary
The `playwright.config.ts` file acts as the central control room, managing browser projects, trace records, and system-wide timeouts.

---

## Session Summary

### Key Takeaways
1. **Setup Automation:** Custom fixtures encapsulate page object creation and navigation setup automatically.
2. **Lifecycle Control:** Hooks structure test execution checkpoints (`beforeAll`, `afterEach`, etc.).
3. **Session Reusability:** Shared authentication saves login tokens locally to bypass login pages across test suites.
4. **Input Decoupling:** Storing data inputs in JSON files keeps test code clean and enables parameterized test loops.
5. **Execution Configuration:** Centralized config files manage parallel runs, timeouts, and browser execution configurations.

### Important Interview Points
* **What is a Playwright Fixture and how does it differ from a Hook?**
  * A hook (`beforeEach`) runs setup code before tests, but lacks an encapsulated teardown flow. A fixture wraps setup and cleanup together using the `yield` keyword. Additionally, fixtures are requested on-demand by tests as arguments, whereas hooks run globally.
* **How do you implement global login state sharing in Playwright?**
  * We write an auth setup test (`auth.setup.ts`) to log in, extract credentials using `context.storageState({ path })`, and configure `playwright.config.ts` to load this saved state file.
* **Why should we parameterize tests using JSON files?**
  * Parameterization runs the same test workflow across different input records, ensuring robust coverage without duplicating code files.
* **How do you control test execution timeout configurations?**
  * We define global limits (e.g. `timeout: 30000`) inside `playwright.config.ts` and set specific step timeouts directly in tests if needed (e.g., `expect(locator).toBeVisible({ timeout: 5000 })`).

### Quick Revision Sheet

| Concept | Playwright Implementation | Key Purpose | Scope |
| --- | --- | --- | --- |
| **Custom Fixture**| `test.extend({ myFixture: ... })` | Setup POM automatically | Requested tests |
| **Hooks** | `beforeEach(async ({ page }) => { ... })` | Run common setup routines | File suite scope |
| **Shared Auth** | `storageState: 'path/auth.json'` | Bypass login screens | Global suite |
| **Test Data** | `import data from './data.json'` | Parameterize input matrices | Loop executions |
| **Env Target** | `process.env.BASE_URL` | Swap environments dynamically| Runtime launch |
| **Config File** | `playwright.config.ts` | Centralize timeout and project rules | Global runner |
