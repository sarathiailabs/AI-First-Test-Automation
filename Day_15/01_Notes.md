# Day 15: Advanced Framework Features

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Logging Framework | 15 mins |
| Parallel Execution | 15 mins |
| Tags | 10 mins |
| Groups | 10 mins |
| Test Suites | 10 mins |
| Custom Reporters | 15 mins |
| Screenshots | 10 mins |
| Videos | 10 mins |
| Traces | 15 mins |
| Debugging | 10 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Set up a **Logging Framework** to record timestamped console outputs.
* Configure **Parallel Execution** threads (Workers) to reduce suite runtimes.
* Organize and run specific tests using **Tags** (e.g. `@smoke`, `@regression`).
* Structure tests logically into **Groups** using `test.describe()`.
* Build and manage modular **Test Suites** for different modules.
* Implement custom report formats by building a **Custom Reporter**.
* Capture diagnostic **Screenshots** and **Videos** natively on test failures.
* Extract and review deep execution logs using Playwright **Traces**.
* Walk through failing test scripts step-by-step using Playwright **Debugging** tools.

---

## Logging Framework

### Definition
A **Logging Framework** is a tool that writes structured, timestamped logs (info, warning, error) to files or consoles, helping developers track execution steps during test runs. *(Logging framework ek software tool hai jo automation test run ke har step ka time-stamped record (logs) console ya file me save karta hai debug-friendly tracking ke liye).*

### Key Concepts
* **Log Levels:**
  * `INFO`: General steps (e.g., "Navigating to Home Page").
  * `WARN`: Non-critical warnings (e.g., "API response took longer than 200ms").
  * `ERROR`: Critical failures (e.g., "Login button did not appear").
* **Benefits:** Consoles get cleaned up; logs are stored persistently in folders for post-run analysis.

### Visual Explanation
**The Bank Ledger Analogy:**
Imagine a cashier at a bank counter. Every time a customer deposits money, verifies their ID, or updates their profile, the cashier writes the transaction details along with the time in a physical logbook. If a mismatch is discovered at the end of the day, they don't guess—they inspect the logbook entry-by-entry. A logging framework is this digital logbook for test runs.

### Example
#### Code
```typescript
import { test } from '@playwright/test';

// Simple custom logger utility class
export class CustomLogger {
  static info(message: string) {
    const timestamp = new Date().toISOString();
    console.log(`[${timestamp}] [INFO]: ${message}`);
  }
  
  static error(message: string) {
    const timestamp = new Date().toISOString();
    console.error(`[${timestamp}] [ERROR]: ${message}`);
  }
}

test('Verification with Custom Logger', async ({ page }) => {
  CustomLogger.info('Launching VJTI Student login page');
  await page.goto('https://api.vjti.edu/login');

  CustomLogger.info('Checking username input visibility');
  const userVisible = await page.locator('#username').isVisible();
  
  if (!userVisible) {
    CustomLogger.error('Username field missing from login page!');
  }
});
```
#### Output
```text
  [2026-06-08T17:05:00.123Z] [INFO]: Launching VJTI Student login page
  [2026-06-08T17:05:01.456Z] [INFO]: Checking username input visibility
```

### Topic Summary
Logging frameworks structure step logs using levels (`INFO`/`ERROR`) alongside timestamps, storing logs for audit tracking.

---

## Parallel Execution

### Definition
**Parallel Execution** is the capability of a test runner to execute multiple test files concurrently across separate browser contexts (called Workers), reducing the total time required to run the test suite. *(Parallel Execution ka matlab hai ek sath multiple test files ko separate browser instances me run karna, jisse overall test run ka time bohot kam ho jata hai).*

### Key Concepts
* **Workers:** Independent OS processes launched by Playwright. Each worker has its own browser context and runs one test file at a time.
* **Fully Parallel:** Playwright config option `fullyParallel: true` splits tests *inside* a single file to run concurrently across workers.
* **CPU Core dependency:** The number of workers is determined by your system's CPU cores.

### Visual Explanation
**The Bank Counter Analogy:**
* **Sequential Run (1 Worker):** A bank has 1 cashier. 10 customers wait in a single line. The cashier serves them one-by-one. Total wait time is long.
* **Parallel Run (4 Workers):** The bank opens 4 separate cash counters. 4 customers are served simultaneously. Total wait time is reduced by 75%.

### Example
#### Configuration (`playwright.config.ts`)
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  fullyParallel: true, // Run individual tests concurrently
  workers: 4,          // Limit concurrent browser workers to 4
});
```

### Common Mistakes
* **Shared test dependencies:** Running tests in parallel that modify the same user record. If Test 1 updates student Amit's name to "Rahul" while Test 2 is simultaneously trying to read Amit's grades, Test 2 will fail due to data collisions. Ensure parallel tests use isolated data records.

### Topic Summary
Parallel execution divides test loads across independent browser workers, configured via the `workers` option inside configs.

---

## Tags

### Definition
**Tags** are custom keyword annotations (like `@smoke` or `@regression`) appended to test descriptions that allow developers to filter and execute specific groups of tests using CLI parameters. *(Tags test descriptions me add kiye jaane wale keyword annotations hain jinke help se specific categories (jaise smoke) ke tests run kiye jaate hain).*

### Key Concepts
* **Filtering:** Use CLI flags like `--grep` to run or exclude tagged tests.
* **Saves Time:** Bypasses slower tests when validating quick build status (e.g. running only `@smoke`).

### Example
#### Code
```typescript
import { test, expect } from '@playwright/test';

test('Verify student portal title @smoke', async ({ page }) => {
  await page.goto('https://api.vjti.edu/portal');
  await expect(page).toHaveTitle(/VJTI Student Portal/);
});

test('Verify complete fees payment grid @regression', async ({ page }) => {
  await page.goto('https://api.vjti.edu/portal/fees');
  // Extensive regression checks
});
```
#### CLI Command
```powershell
# Run only tests tagged with @smoke
npx playwright test --grep "@smoke"

# Exclude tests tagged with @regression
npx playwright test --grep-invert "@regression"
```

### Topic Summary
Tags filter execution blocks selectively at runtime using command line arguments.

---

## Groups

### Definition
**Groups** represent organizing related test cases inside a structured container block using the `test.describe()` function. *(Groups ka use hum similar test cases ko aapas me organize karne ke liye 'test.describe' block me group karke karte hain).*

### Key Concepts
* **Scoping:** Hooks defined inside a `test.describe` group apply *only* to tests within that group, avoiding global hook pollution.
* **Logical nesting:** Grouping tests by feature (e.g. "Login Features Group", "Dashboard Grid Group").

### Example
#### Code
```typescript
import { test, expect } from '@playwright/test';

test.describe('Student Fees Portal Groups', () => {
  // Hook scope restricted to this describe group only
  test.beforeEach(async ({ page }) => {
    await page.goto('https://api.vjti.edu/portal/fees');
  });

  test('Verify unpaid fees alert', async ({ page }) => {
    await expect(page.locator('.unpaid-alert')).toBeVisible();
  });

  test('Verify invoice download', async ({ page }) => {
    await expect(page.locator('#download-invoice')).toBeEnabled();
  });
});
```

### Topic Summary
Groups namespace tests into logical blocks using `test.describe()`, restricting hook scopes.

---

## Test Suites

### Definition
A **Test Suite** is a logical collection of test files grouped by feature or module (e.g. billing suite, profile suite) configured to execute against specific environments. *(Test Suite related test files ka ek set hai jo kisi specific portal feature (jaise payment flow) ko complete check karta hai).*

### Key Concepts
* **Directory Isolation:** Suites are organized into sub-folders like `tests/api/`, `tests/ui/`, or `tests/auth/`.
* **Playwright Projects:** Can configure target test match directories for specific projects in the configuration file.

### Example
#### Structure
```text
  tests/
  ├── auth/
  │   ├── login.spec.ts
  │   └── signup.spec.ts
  └── payments/
      ├── billing.spec.ts
      └── refund.spec.ts
```
#### CLI Run Commands
```powershell
# Run only the payments suite
npx playwright test tests/payments/

# Run the complete UI authentication suite
npx playwright test tests/auth/
```

### Topic Summary
Test suites categorize automation scripts into directory folders to support targeted pipeline runs.

---

## Custom Reporters

### Definition
A **Custom Reporter** is a custom JavaScript class that implements Playwright's `Reporter` interface to customize, format, and export test run results (e.g. sending slack alerts, writing custom JSON files). *(Custom Reporter ek script hai jo test results ko customize karke display karne ya external reports (jaise slack alert file) banane ke kaam aati hai).*

### Key Concepts
* **Lifecycle Listener Hooks:** Playwright triggers reporter methods automatically during lifecycle steps:
  * `onBegin()`: Triggered when test run starts.
  * `onTestBegin()`: Triggered when an individual test block starts.
  * `onTestEnd()`: Triggered when a test finishes (returns success status).
  * `onEnd()`: Triggered after all tests run complete.

### Example
#### Custom Reporter File (`my-reporter.ts`)
```typescript
import { Reporter, TestCase, TestResult, FullResult } from '@playwright/test/reporter';

export default class CustomProgressReporter implements Reporter {
  onBegin(config, suite) {
    console.log(`[Suite Start]: Running ${suite.allTests().length} tests...`);
  }

  onTestBegin(test: TestCase) {
    console.log(`[Test Running]: ${test.title}`);
  }

  onTestEnd(test: TestCase, result: TestResult) {
    console.log(`[Test Finished] [${result.status}]: ${test.title}`);
  }

  onEnd(result: FullResult) {
    console.log(`[Suite Complete] Status: ${result.status}`);
  }
}
```
#### Configuration (`playwright.config.ts`)
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [['./my-reporter.ts']], // Register custom reporter
});
```

### Topic Summary
Custom reporters hook into runner lifecycle events to output custom logs or notifications.

---

## Screenshots

### Definition
**Screenshots** represent image snapshots captured from the browser screen during test execution, primarily used to diagnose UI layout states on failure. *(Screenshots ka matlab hai browser window ki photo capture karna jab koi validation test fail ho jaye).*

### Key Concepts
* **Nail down failures:** Seeing a screenshot of the layout when a button click failed is faster than guessing from console stack traces.
* **Natively Automated:** Playwright configures automatic capture on failure.

### Example
#### Config setup (`playwright.config.ts`)
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  use: {
    // Automatically capture screenshot only if a test block fails
    screenshot: 'only-on-failure',
  },
});
```
#### Manual in-line script command
```typescript
await page.screenshot({ path: 'screenshots/dashboard.png', fullPage: true });
```

### Topic Summary
Screenshots document visual failures, configured to capture layout states on crash events.

---

## Videos

### Definition
**Videos** represent screen recordings capturing the visual interactions of a test execution, providing QA teams with step-by-step video playback of failures. *(Videos ka matlab hai test execution ka video record karna taaki step-by-step user interactions ko review kiya sake).*

### Key Concepts
* **Trace flow animation:** Video playback records scroll lags, loading states, and dynamic elements transitions.
* **Auto-cleanup:** Video configurations can delete recordings of passing tests automatically to save disk space.

### Example
#### Config setup (`playwright.config.ts`)
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  use: {
    // Record video on retry or failure, and auto-delete on success
    video: 'retain-on-failure',
  },
});
```

### Topic Summary
Video records track interactive step transitions, retained selectively on test failures.

---

## Traces

### Definition
A Playwright **Trace** is a comprehensive ZIP diagnostic file containing execution steps, timeline metrics, page DOM snapshots at each step, console logs, and network network calls, acting as a complete black box flight recorder for test runs. *(Playwright Trace ek detailed diagnostic file hai jisme execution timelines, DOM snapshots, network requests, aur log records hote hain debug karne ke liye).*

### Key Concepts
* **Black Box Recorder:** Captures everything. You can hover over the execution timeline to see exactly what the DOM looked like before and after a click.
* **Trace Viewer:** Opened via the command line to inspect trace files.

### Visual Explanation
**The Flight Black Box Analogy:**
When an airplane encounters a flight error, investigators inspect the Black Box. It records pilot dialogues, flight coordinates, engine pressure, and cabin temperatures. They can reconstruct the entire flight path. A Playwright Trace acts as this black box, recording every locator search, hover, click, and console message.

### Example
#### Config setup (`playwright.config.ts`)
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  use: {
    // Record trace details if a test fails in the pipeline
    trace: 'retain-on-failure',
  },
});
```
#### CLI Command to view trace file
```powershell
# Launch the Trace Viewer UI dashboard
npx playwright show-trace test-results/portal-test-failures/trace.zip
```

### Topic Summary
Traces log comprehensive diagnostic logs, networks, and DOM states, viewed using the Trace Viewer tool.

---

## Debugging

### Definition
**Debugging** is the process of using diagnostic tools (like Playwright Inspector) to execute test scripts line-by-line, inspect page elements, and test selectors interactively. *(Debugging test code ko line-by-line execute karke errors find karne aur selectors correct karne ka inspector setup hai).*

### Key Concepts
* **Playwright Inspector:** Opens a UI panel alongside the browser where you can click "Step Over" to run one line at a time.
* **Code Breakpoints:** Inserting `page.pause()` pauses execution at a specific line.

### Example
#### In-line pause command
```typescript
test('Verify interactive table', async ({ page }) => {
  await page.goto('https://api.vjti.edu/portal');
  // Pause execution and launch Playwright Inspector here
  await page.pause();
  await page.locator('#marks-table').click();
});
```
#### CLI Debug Command
```powershell
# Run all tests in debug inspector mode
npx playwright test --debug
```

### Topic Summary
Debugging steps execute tests slowly inside the Inspector tool, pausing at breakpoint triggers to inspect element locations.

---

## Session Summary

### Key Takeaways
1. **Timestamped logging:** Custom log frameworks track step markers with timestamp details.
2. **Concurrencies:** Parallel execution splits test files across separate worker profiles.
3. **Filtering & Scopes:** Tag annotations filter CLI targets, and `describe` groups restrict hook bounds.
4. **Diagnostic records:** Screenshots, videos, and trace black boxes document failures.
5. **Interactive checks:** Playwright Inspector debug commands allow step-by-step execution.

### Important Interview Points
* **How do you configure tests to run in parallel in Playwright?**
  * We configure `fullyParallel: true` and define `workers: <number>` inside the `playwright.config.ts` file.
* **What is a Playwright Trace and how do you open it?**
  * A Trace is a detailed ZIP file recording DOM screenshots, network requests, console messages, and source maps for each step. We record it using `trace: 'retain-on-failure'` and view it using `npx playwright show-trace path/to/trace.zip`.
* **What is the difference between test.describe() and test() tags?**
  * `test.describe()` defines a test group (block) to restrict setup hook scopes. Tags (like `@smoke`) are string annotations added to descriptions to filter CLI runs.
* **How does a Custom Reporter track test failures?**
  * It implements the `Reporter` interface, listening to lifecycle hooks like `onTestEnd(test, result)`. If `result.status` equals `'failed'`, the reporter executes alert actions.

### Quick Revision Sheet

| Feature | Config parameter | CLI command / Code pattern | Primary Benefit |
| --- | --- | --- | --- |
| **Logger** | Custom class | `CustomLogger.info("message")` | Persistent timestamps |
| **Parallel** | `fullyParallel: true` | `npx playwright test --workers=4` | Minimizes execution times |
| **Tags** | `@smoke` annotation | `npx playwright test --grep "@smoke"` | Filter CLI runs |
| **Groups** | `test.describe` | `test.describe('group name', () => {})` | Restricts hook bounds |
| **Trace** | `trace: 'retain-on-failure'`| `npx playwright show-trace path.zip` | Black box diagnostics |
| **Debug** | N/A | `npx playwright test --debug` | Step-by-step inspector |
| **Custom Rep**| `reporter: [['./rep.ts']]` | Implements `Reporter` interface | Sends alert logs |
