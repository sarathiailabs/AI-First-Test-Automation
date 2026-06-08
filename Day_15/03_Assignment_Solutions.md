# Day 15: Advanced Framework Features – Solutions

This file contains solutions for the theory questions and practical programming assignments assigned for Day 15.

---

## Theory Question Solutions

### Question 1
What is **Parallel Execution** in Playwright? How does Playwright allocate tests across browser "Workers"?

### Answer
* **Parallel Execution:** Running multiple test files simultaneously to reduce total execution runtime.
* **Workers Allocation:** Playwright runs test files in separate processes called Workers. Each Worker is a separate OS process that spins up its own clean browser instance. By default, files are run in parallel, while tests inside a single file are run sequentially within the same worker context (unless `fullyParallel: true` is configured).

---

### Question 2
What is a Playwright **Trace**? What diagnostic files are captured inside a trace zip file, and how is it opened?

### Answer
* **Playwright Trace:** A diagnostic black box zip file that records detailed snapshots of a test run.
* **Captured Diagnostics:** DOM screenshots of each step, network request/response headers and bodies, console logs, execution time metrics, source code mapping, and locator parameters.
* **How Opened:** Download the ZIP trace file and run command:
  ```powershell
  npx playwright show-trace path/to/trace.zip
  ```

---

### Question 3
How does a **Custom Reporter** listen to test lifecycle events? Name the core callback methods implementation hooks.

### Answer
* **How it listens:** By implementing the Playwright `Reporter` interface. Playwright automatically calls specific methods in the reporter class during execution milestones.
* **Core Hooks:**
  * `onBegin(config, suite)`: Runs before test execution starts.
  * `onTestBegin(test)`: Runs when a specific test begins.
  * `onTestEnd(test, result)`: Runs when a test finishes, capturing the final status (`passed`, `failed`, `skipped`).
  * `onEnd(result)`: Runs after the entire test suite completes.

---

### Question 4
What is the difference between running tests in **Debug Mode** (`--debug`) and checking failures using a **Trace Viewer**?

### Answer
* **Debug Mode (`--debug`):** An interactive, real-time debugging environment where tests execute slowly, allowing the user to step through lines of code live, hover over elements, and test locator expressions.
* **Trace Viewer:** A post-execution analysis tool that runs offline using a recorded ZIP file. It does not run live code; it only replays DOM recordings and network states captured during a past execution, which is ideal for inspecting CI pipeline failures.

---

### Question 5
Explain how parallel execution can cause data collision bugs. How do you design tests to avoid sharing database records?

### Answer
* **Data Collision:** If two workers run different tests at the same time and both modify the same database entity (e.g. updating student ID 101), the database state becomes inconsistent, causing assertions to fail.
* **Design Solution:** Implement test isolation. Ensure each test creates its own unique entity (e.g. using dynamic IDs like `Student_XYZ123`), operates only on that entity, and deletes it at the end of the test.

---

## Programming Assignment Solutions

### Assignment 1: Configuring Parallel Workers

#### Solution Code (`playwright.config.ts`)
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './tests',
  fullyParallel: true, // Run individual tests inside files concurrently
  
  // Set workers dynamically based on pipeline context
  workers: process.env.CI ? 1 : 3, 

  use: {
    screenshot: 'only-on-failure',
  }
});
```

---

### Assignment 2: Custom Console Progress Reporter

#### Solution Code (`vjti-reporter.ts`)
```typescript
import { Reporter, TestCase, TestResult, FullResult } from '@playwright/test/reporter';

export default class VJTIProgressReporter implements Reporter {
  
  // Triggered when the suite starts
  onBegin(config, suite) {
    console.log(`\n--- VJTI SUITE STARTING: ${suite.allTests().length} tests queued ---`);
  }

  // Triggered when an individual test begins
  onTestBegin(test: TestCase) {
    console.log(`[STARTING]: ${test.title}`);
  }

  // Triggered when a test finishes
  onTestEnd(test: TestCase, result: TestResult) {
    console.log(`[FINISHED] [${result.status}]: ${test.title} (Duration: ${result.duration}ms)`);
  }

  // Triggered when all tests are complete
  onEnd(result: FullResult) {
    console.log(`--- VJTI SUITE COMPLETE: Overall Status = [${result.status}] ---\n`);
  }
}
```

##### How to register in `playwright.config.ts`
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [['./vjti-reporter.ts']], // Register custom class file
});
```

---

### Assignment 3: Playwright Trace Configuration

#### Solution Code (`playwright.config.ts` configuration)
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  use: {
    // Record traces and screenshots only when tests fail
    trace: 'retain-on-failure',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure'
  }
});
```

#### Diagnostic Guide (`trace-guide.md`)
```markdown
# Playwright Trace Diagnostics Guide

Follow these steps to analyze failing tests captured in CI pipelines:

1. **Locate Trace File:** Look inside the build artifacts folder or download the failure logs. You will find a `trace.zip` file saved in the `test-results/` directory.
2. **Open Trace Viewer:** Launch your local terminal and run the CLI command:
   ```bash
   npx playwright show-trace path/to/trace.zip
   ```
3. **Inspect Timelines:**
   - **Actions Tab:** View the list of Playwright actions (click, fill) executed step-by-step.
   - **DOM Snapshot:** Hover over the timeline to see what the browser screen rendered at each step.
   - **Network Log:** Check network payloads and response status codes for API calls.
   - **Console Output:** View console warnings and errors thrown during the run.
```
