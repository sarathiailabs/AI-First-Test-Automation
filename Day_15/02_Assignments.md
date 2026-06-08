# Day 15: Advanced Framework Features – Assignments

This assignment file contains theoretical questions and practical programming tasks designed to reinforce logging frameworks, parallel execution, test tagging, custom reporter design, screenshots/videos hooks, trace analysis, and inspector debugging.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is **Parallel Execution** in Playwright? How does Playwright allocate tests across browser "Workers"?

### Question 2 🔥 **Frequently Asked**
What is a Playwright **Trace**? What diagnostic files are captured inside a trace zip file, and how is it opened?

### Question 3 📌 **Important**
How does a **Custom Reporter** listen to test lifecycle events? Name the core callback methods implementation hooks.

### Question 4 📌 **Important**
What is the difference between running tests in **Debug Mode** (`--debug`) and checking failures using a **Trace Viewer**?

### Question 5 💡 **Good to Know**
Explain how parallel execution can cause data collision bugs. How do you design tests to avoid sharing database records?

---

## Practical Assignments

### Assignment 1: Configuring Parallel Workers

* **Interview Relevance:** Optimizing parallel threads is a must-know for CI pipeline setups.
* **Difficulty Level:** Easy-Intermediate
* **Concepts Covered:** config `workers`, `fullyParallel`, parallel projects setting.

#### Problem Statement
Modify the configuration file `playwright.config.ts` to configure parallel execution rules: run tests inside files in parallel, use 3 workers locally, and limit workers to 1 in CI environments.

#### Requirements
1. Modify `playwright.config.ts`.
2. Configure `fullyParallel: true`.
3. Configure `workers` dynamically:
   - If `process.env.CI` is defined, set workers to `1` (serial pipeline runs).
   - If `process.env.CI` is not defined, set workers to `3` (parallel local runs).
4. Export the configuration.

#### Expected Output
```text
  ✓ Config: Workers set to 3 (Local Parallel Execution Mode)
```

---

### Assignment 2: Custom console Progress Reporter

* **Interview Relevance:** Designing custom reporters is a common task in enterprise setups where test results must feed into external metric tools (Allure, Slack, Jira).
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `Reporter` interface, event callbacks implementation.

#### Problem Statement
Write a custom reporter file named `vjti-reporter.ts` that implements the Playwright `Reporter` interface. The reporter must print clear messages when a test starts, when a test finishes, and a summary when the entire run completes.

#### Requirements
1. Create `vjti-reporter.ts`.
2. Implement methods:
   - `onTestBegin(test)`: Log `[STARTING]: testTitle`.
   - `onTestEnd(test, result)`: Log `[FINISHED] [status]: testTitle`.
   - `onEnd(result)`: Log `[SUITE COMPLETE] status: status`.
3. Export the reporter class as default.
4. Modify `playwright.config.ts` to register the custom reporter path.

#### Expected Output (in terminal during test execution)
```text
  [STARTING]: Verify student login page
  [FINISHED] [passed]: Verify student login page
  [SUITE COMPLETE] status: passed
```

---

### Assignment 3: Playwright Trace configuration

* **Interview Relevance:** Debugging flaky pipeline failures requires inspecting trace zip logs. Tests ability to configure traces and analyze DOM state histories.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `trace: 'retain-on-failure'`, show-trace command.

#### Problem Statement
Configure the Playwright setup to record trace diagnostic zip files on failure and describe the command used to review this trace in the browser viewer.

#### Requirements
1. Configure `playwright.config.ts` `use` parameters:
   - Set `trace` to `'retain-on-failure'`.
   - Set `screenshot` to `'only-on-failure'`.
2. Write a short text document named `trace-guide.md` describing how a QA team member runs the trace viewer tool to debug a failed pipeline run trace file saved in `test-results/`.

#### Hints
* The trace viewer command is: `npx playwright show-trace path/to/trace.zip`.
