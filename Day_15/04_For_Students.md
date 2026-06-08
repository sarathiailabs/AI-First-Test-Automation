# Day 15: Advanced Framework Features – Student Revision Notes

This revision sheet provides a quick-revision summary of framework settings, parallel execution configuration, tags, trace viewers, and debugging inspector steps for Day 15.

---

## Topic Revision

### 1. Logging Framework
* **Definition:** Setting up timestamped console logs to track execution details during test runs. *(Automation execution steps ko level labels (INFO/ERROR) aur timestamps ke sath log karna).*
* **Example Syntax:**
  ```typescript
  console.log(`[${new Date().toISOString()}] [INFO]: Navigating page`);
  ```
* **Key Points:**
  * Avoids dependency on default console prints.
  * Essential for debugging CI pipelines.

---

### 2. Parallel Execution
* **Definition:** Running test files concurrently using multiple processes called Workers. *(Multiple files ko ek sath different browsers (workers) me execute karna).*
* **Example Config:**
  ```typescript
  export default defineConfig({
    fullyParallel: true,
    workers: 3
  });
  ```
* **Common Mistakes:** Sharing a single database record across parallel tests, causing collisions. Use isolated test data.

---

### 3. Tags
* **Definition:** String annotations (e.g. `@smoke`, `@regression`) inside test descriptions to filter CLI runs. *(Tests filter karne ke liye descriptions me tag keywords add karna).*
* **Example CLI Command:**
  ```bash
  npx playwright test --grep "@smoke"
  ```
* **Key Points:**
  * Runs targeted subsets of tests (e.g., running smoke tests quickly on check-in).

---

### 4. Groups
* **Definition:** Organizing related test cases into container blocks using `test.describe()`. *(Related tests ko describe block me group karna).*
* **Example Block:**
  ```typescript
  test.describe('Payment Flows', () => {
    test('Pay bills', async ({ page }) => { ... });
  });
  ```
* **Key Points:**
  * Restricts setup/teardown hook scopes to tests within the group.

---

### 5. Test Suites
* **Definition:** Directory folders (e.g. `tests/auth/`, `tests/api/`) organizing test files by module. *(Related test files ko folders me divide karna).*
* **Example Run Command:**
  `npx playwright test tests/auth/`

---

### 6. Custom Reporters
* **Definition:** Creating custom reports by implementing the Playwright `Reporter` interface. *(Supermarket receipt ki tarah test status report format customize karna).*
* **Key Lifecycle Hooks:**
  `onBegin()`, `onTestBegin()`, `onTestEnd()`, `onEnd()`

---

### 7. Screenshots & Videos
* **Definition:** Capturing visual snapshots and screen recordings of browser interactions to debug failing tests. *(Failing tests ki browser screenshots aur videos save karna).*
* **Example Config:**
  `screenshot: 'only-on-failure'`, `video: 'retain-on-failure'`

---

### 8. Traces
* **Definition:** ZIP archive file recording all browser steps, DOM snapshots, network calls, and console logs. *(Test run ka digital flight recorder / black box).*
* **Example CLI Command:**
  ```bash
  npx playwright show-trace test-results/trace.zip
  ```

---

### 9. Debugging
* **Definition:** Pausing execution and stepping through code line-by-line using Playwright Inspector. *(Test actions ko step-by-step debug check panels ke through inspect karna).*
* **Example Code Breakpoint:**
  `await page.pause();`
* **Example CLI Command:**
  `npx playwright test --debug`

---

## Assignment Summary

During this session, we practice:
1. **Configuring Parallel Workers:** Dynamically setting workers based on environment contexts.
2. **Custom Progress Reporter:** Building a console reporter class that implements the `Reporter` interface.
3. **Playwright Trace Viewer Guide:** Configuring trace collections and writing a trace debugging guide.

---

## Quick Revision Sheet

| Feature | Code Pattern | CLI Option | Key Benefit |
| --- | --- | --- | --- |
| **Parallel** | `fullyParallel: true` | `--workers=4` | Cuts execution run times |
| **Tags** | `@smoke` annotation | `--grep "@smoke"` | Run targeted tests selectively |
| **Groups** | `test.describe` | N/A | Isolates hook scopes |
| **Trace** | `trace: 'retain-on-failure'` | `show-trace` | Black box diagnostics |
| **Debug** | `await page.pause()` | `--debug` | Step-by-step code inspector |
| **Custom Rep**| Implements `Reporter` | `reporter: [['./rep.ts']]`| Custom outputs & alerts |

---

## Important Takeaways

1. **CPU Worker Limit:** Locally, limit workers based on CPU cores. In CI, limit workers to 1 to avoid resource bottleneck failures.
2. **Zero-Interaction Trace Viewer:** Traces are ideal for troubleshooting CI pipeline crashes offline.
3. **Independent Data:** Parallel tests must always run on isolated data entities.
