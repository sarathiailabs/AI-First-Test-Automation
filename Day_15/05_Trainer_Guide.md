# Day 15: Advanced Framework Features – Trainer Guide

This Delivery Guide helps instructors teach Day 15: Advanced Framework Features. Follow the lesson workflows, whiteboard diagrams, and coding checkpoints below.

---

## Session Opening

### Welcome Script
> *"Good evening, class! Welcome to Day 15. We have already built Page Objects and configured test data loops. Today, we are focusing on advanced framework capabilities that QA teams use in enterprise projects. We will learn how to run tests in parallel to speed up builds, tag tests, implement custom reporters, capture failure screenshots, and inspect execution traces using Playwright's trace black box recorder. Let's begin!"*

### Session Goal
By the end of this session, trainers will help students manage concurrent executions, organize tests into suites, write custom progress reporters, configure trace recording parameters, and use step-by-step inspector tools.

### Motivation
> *"When a test fails in a local run, we see the console. But what if a test fails randomly at 2:00 AM on a remote CI pipeline? You can't see the screen. Using Playwright Traces, we record a ZIP file that acts as a black box flight recorder. We can download it, open the Trace Viewer, and watch a replay of the DOM and network calls at each step. This makes debugging CI failures incredibly easy!"*

---

## Topic 1: Logging Framework

### Trainer Introduction
> *"Logging is like keeping a driver's logbook. Instead of guessing when a test was launched or where it crashed, we write timestamped records (INFO/ERROR) to a file. This gives us a clear history of what the test script did during its run."*

### Student Engagement Questions
1. *"What is the difference between standard console.log() and a structured logging framework?"* (Answer: Timestamps, log levels, persistent storage).

### Topic Recap
Log frameworks structure execution records using levels (`INFO`/`ERROR`) and timestamps.

### Transition Script
> *"Logs show us what happened step-by-step. But running tests one-by-one is slow. Let's learn how to run them concurrently using Parallel Execution."*

---

## Topic 2: Parallel Execution

### Trainer Introduction
> *"Parallel Execution means opening multiple browser windows (workers) to run different tests at the same time. If one worker takes 10 seconds to run a test, 4 workers running 4 tests will finish the suite in 10 seconds instead of 40."*

### Student Engagement Questions
1. *"If a bank has 4 cash counters instead of 1, how does it affect the queue? How is a cashier like a Playwright Worker?"*
2. *"What is a data collision? What happens if Test 1 updates a record that Test 2 is trying to read at the same time?"*

### Whiteboard Teaching
```text
  Sequential (1 Worker):
   [ Worker 1 ] ──► Test 1 ──► Test 2 ──► Test 3 (Total: 30s)
  
  Parallel (3 Workers):
   [ Worker 1 ] ──► Test 1 (10s)
   [ Worker 2 ] ──► Test 2 (10s)  (Total: 10s)
   [ Worker 3 ] ──► Test 3 (10s)
```

### Topic Recap
Parallel execution divides test loads across independent CPU workers concurrently.

### Transition Script
> *"Parallel execution runs tests quickly. But what if we only want to run a specific group of tests, like our Smoke tests? We filter them using Tags."*

---

## Topic 3: Tags

### Trainer Introduction
> *"Tags are keyword annotations (like `@smoke` or `@regression`) added to test descriptions. They allow you to run or exclude specific tests using command line flags."*

### Student Engagement Questions
1. *"If you tag airport bags with a bright green label, does it help prioritize them? How is `--grep` like that color tag?"*

### CLI Demonstration
Show how tags are filtered:
```powershell
npx playwright test --grep "@smoke"
```

### Topic Recap
Tags filter execution blocks at runtime using command line parameters.

### Transition Script
> *"Tags group individual tests. To organize related tests structurally inside our code files, we use Groups."*

---

## Topic 4: Groups

### Trainer Introduction
> *"We group related tests inside a container using `test.describe()`. This helps organize test suites and restricts the scope of hooks (like `beforeEach`) so they only run for tests within that group."*

### Whiteboard Teaching
```text
  test.describe('Payment Tests', () => {
    test.beforeEach(...) // Runs only for tests inside this block
    test('Pay bills')
    test('View invoice')
  })
```

### Topic Recap
Groups namespace related test scripts using `test.describe()`.

### Transition Script
> *"We have grouped tests inside our code. Now let's look at how we organize files into folders to create Test Suites."*

---

## Topic 5: Test Suites

### Trainer Introduction
> *"A Test Suite is a folder containing related test files (e.g. `tests/auth` for login scripts, `tests/api` for API validations). Organizing files into folders makes it easy to run specific modules from the CLI."*

### Topic Recap
Test suites group automation scripts by directory to support modular execution.

### Transition Script
> *"When we run these suites, Playwright prints results to the terminal. But what if we want to customize this output or send alerts to Slack? We build a Custom Reporter."*

---

## Topic 6: Custom Reporters

### Trainer Introduction
> *"A Custom Reporter implements Playwright's `Reporter` interface, listening to test milestones (like when a test starts or finishes) to format and export results to external tools."*

### Live Coding Demonstration
#### Step 1: Type
```typescript
class CustomReporter {
  onTestEnd(test, result) {
    console.log(`[STATUS]: ${test.title} -> ${result.status}`);
  }
}
```

### Topic Recap
Custom reporters listen to test lifecycle events to generate custom outputs.

### Transition Script
> *"Reporters log test statuses. When a test status is 'failed', how do we capture visual evidence? We use Screenshots."*

---

## Topic 7: Screenshots

### Trainer Introduction
> *"Screenshots capture image snapshots of the browser window on failure. This visual record helps QA teams verify if a button click failed because the button was hidden or missing."*

### Topic Recap
Screenshots document visual layouts on crash events automatically.

### Transition Script
> *"Screenshots are great, but they are static images. To see the actual interactions and animations before a crash, we record Videos."*

---

## Topic 8: Videos

### Trainer Introduction
> *"Videos record the entire test interaction. Reviewing video playbacks is ideal for checking scroll delays, transition animations, and loader spin times before a failure."*

### Topic Recap
Videos record browser interactions, retained selectively on test failures.

### Transition Script
> *"Videos show us the screen. But what about network calls, console logs, and source code execution steps? We capture all of them using Traces."*

---

## Topic 9: Traces

### Trainer Introduction
> *"A Playwright Trace is a ZIP file containing screenshots, network payloads, console messages, and source code actions. It is a complete black box flight recorder that you inspect using the Trace Viewer tool."*

### Whiteboard Teaching
Draw the Trace Viewer panels:
```text
  +-------------------------------------------------------+
  |                   Timeline (Hover to view DOM)        |
  +----------------------+--------------------------------+
  | Actions Log          | Live DOM / Network Inspector   |
  | - Click login button | Header details, body payloads  |
  +----------------------+--------------------------------+
```

### Topic Recap
Traces record comprehensive diagnostic data for offline analysis.

### Transition Script
> *"Trace files are great for reviewing past runs. But if you want to inspect a test live and step through code line-by-line, you use Debugging."*

---

## Topic 10: Debugging

### Trainer Introduction
> *"Debugging pauses test execution and opens the Playwright Inspector. You can click 'Step Over' to run your test one line at a time, checking element states and testing selectors live."*

### Live Coding Demonstration
#### Step 1: Type
```typescript
test('Debug demo', async ({ page }) => {
  await page.goto('https://api.vjti.edu');
  await page.pause(); // Breakpoint triggers Inspector here
});
```

### Topic Recap
Debugging executes tests line-by-line inside the Inspector tool using breakpoints.

---

## Session Closing

### Session Summary
* We configured dynamic worker concurrncies for parallel runs.
* We tagged and grouped tests using CLI filters and describe blocks.
* We implemented custom progress reporters.
* We captured failure screenshots, videos, and trace zip logs.
* We ran step-by-step live inspections using the Playwright Inspector tool.

### Knowledge Check Questions
1. *"What is the main benefit of parallel execution? (Answer: Reduces execution time)."*
2. *"How do you launch the Trace Viewer? (Answer: npx playwright show-trace path/to/trace.zip)."*
3. *"Why is the beforeEach hook inside test.describe isolated? (Answer: It only runs for tests within that group)."*
4. *"What command runs tests in debug mode? (Answer: npx playwright test --debug)."*
5. *"Why is process.env.CI used to limit workers to 1? (Answer: To avoid CI pipeline bottlenecks)."*

### Homework Guidance
Instruct students to complete the `Day_15` assignments:
1. Dynamic parallel worker configuration.
2. Custom reporter implementation inside `vjti-reporter.ts`.
3. Setting up trace configs and writing a trace diagnostics guide.

### Next Session Preview
In Day 16, we move to DevOps, learning how to dockerize our automation framework and configure continuous integration pipelines in GitHub Actions.
