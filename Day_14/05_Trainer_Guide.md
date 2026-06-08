# Day 14: Fixtures + Hooks + Test Data – Trainer Guide

This delivery handbook helps instructors conduct Day 14: Fixtures + Hooks + Test Data. Follow the timelines, scripts, and engagement questions below.

---

## Session Opening

### Welcome Script
> *"Good evening, class! Welcome to Day 14. In our last session, we created Page Object classes. But in our test files, we still have to write `const loginPage = new LoginPage(page)` in almost every single test. That is repetitive boilerplate code. Today, we are learning how to automate this using Playwright Fixtures. We will also learn how to run lifecycle hooks, share login credentials globally, import JSON data sheets, and configure base settings. Let's get started!"*

### Session Goal
Provide a comprehensive understanding of automated setups and configurations. Explain how to design fixtures, implement hooks, manage shared authentication, parameterize test cases using JSON inputs, manage environments dynamically, and control global timeouts.

### Motivation
> *"When you run a large suite of 500 tests, logging into the application 500 times dynamically takes about 45 minutes of pipeline duration. Using Shared Authentication (saving cookies once and injecting them), we run the login flow exactly once, saving 40 minutes of execution time. That is the kind of optimization that senior QA engineers bring to industry projects!"*

---

## Topic 1: Fixtures

### Trainer Introduction
> *"A Playwright fixture is a pre-configured environment. Think of it as a rented wedding hall. Instead of arriving early to sweep floors, cook food, and set up speakers yourself before every event, you rent a hall that comes pre-set. You just show up, run the wedding, and leave. Playwright page objects are managed by fixtures exactly like this. The setup and teardown are handled automatically."*

### Student Engagement Questions
1. *"Why is it repetitive to write `new LoginPage(page)` inside every test block?"*
2. *"What does the yield keyword do inside custom fixtures?"* (Answer: It passes control to the test script, acting as a boundary between setup and teardown).

### Whiteboard Teaching
Draw the fixture scope boundary:
```text
  [ FIXTURE setup ]    ──► [ TEST script execution ] ──► [ FIXTURE teardown ]
  (Instantiate classes,     (Test uses page object           (Run cleanup tasks,
   pre-navigate URL)         without creating it)             close connections)
```

### Topic Recap
Fixtures extend base runner suites to manage setups/teardowns on-demand.

### Transition Script
> *"Fixtures manage environments on-demand. But what if we want to run setup steps before *every* test globally inside a file? We use Hooks."*

---

## Topic 2: Hooks

### Trainer Introduction
> *"Hooks are lifecycle managers. Think of a college chemistry lab: Before the lab course starts, the teacher prepares chemicals (`beforeAll`). Before each student starts an experiment, they clean their test tubes (`beforeEach`). After each experiment, they rinse their tubes (`afterEach`). After the entire course is over, the teacher locks the lab door (`afterAll`). Hooks manage test cycles exactly like this."*

### Student Engagement Questions
1. *"If you want to clear a database before running the first test in a file, which hook should you use?"* (Answer: beforeAll).
2. *"If you want to take a failure screenshot after a test crashes, which hook should you use?"* (Answer: afterEach).

### Whiteboard Teaching
```text
  beforeAll (1 time) ──► beforeEach (per test) ──► TEST ──► afterEach (per test) ──► afterAll (1 time)
```

### Topic Recap
Hooks define lifecycle execution checkpoints before or after test blocks.

### Transition Script
> *"Hooks and fixtures set up tests. Now, let's learn how we use them to share login sessions across all tests: Shared Authentication."*

---

## Topic 3: Shared Authentication

### Trainer Introduction
> *"Shared Authentication saves active login cookies and tokens into a JSON file once. When subsequent tests launch, Playwright injects this cookie file directly into the browser context. The browser instantly acts as if you are logged in, skipping the login screen entirely."*

### Student Engagement Questions
1. *"If you have a water park wristband, do you need to show your ID and pay cash at every ride? How is storage state like a wristband?"*

### Whiteboard Teaching
```text
  [ Step 1: auth.setup.ts ] ──► login UI ──► context.storageState() ──► playwright/.auth/user.json
                                                                                     │
  [ Step 2: Test scripts ]  ◄── Injects user.json automatically ◄────────────────────┘
```

### Topic Recap
Shared authentication bypasses login UI steps by loading saved context states globally.

### Transition Script
> *"Bypassing logins saves time. What if we want to run the same login workflow across 20 different student usernames? We use Test Data Management."*

---

## Topic 4: Test Data Management

### Trainer Introduction
> *"Test Data Management means separating our data inputs (like usernames, passwords) from the code scripts and saving them in JSON files. We import the JSON data and run our test template inside a loop, executing a data-driven validation suite."*

### Live Coding Demonstration
#### Step 1: Type
Show loop configuration:
```typescript
import testData from './data.json';
for (const data of testData) {
  test(`Verify data: ${data.name}`, async () => {
    console.log(`Running with: ${data.username}`);
  });
}
```

### Topic Recap
JSON file data parameterizes test cases, running test templates across variable input sets.

### Transition Script
> *"Decoupling data inputs keeps tests clean. How do we decouple target environment URLs, like Staging vs Production? We use Environment Management."*

---

## Topic 5: Environment Management

### Trainer Introduction
> *"Environment Management lets us switch our base URL targets dynamically without modifying code. We load the host address from environment variables (`process.env`), allowing developers to run tests on localhost while pipelines run tests on Staging."*

### Student Engagement Questions
1. *"How do you change a TV channel from sports to news? Does the TV machine change? How is switching test target hosts similar?"*

### Topic Recap
Environment management resolves targeted servers at runtime using external variable configurations.

### Transition Script
> *"Finally, where do we configure the browsers, parallel threads, and test timeouts that run our framework? We manage them in Configuration Files."*

---

## Topic 6: Configuration Files

### Trainer Introduction
> *"The `playwright.config.ts` file is the central control room of your framework. Here, you define how long a test can run before it times out, how many times a failed test should retry, and which browsers to run."*

### Whiteboard Teaching
Draw the timeout differences:
```text
  [ Global Timeout: 30 seconds ] (Max test duration)
    ├─► beforeEach hook
    ├─► test action steps
    ├─► [ Expect Timeout: 5 seconds ] (Max time to wait for a locator check)
    └─► afterEach hook
```

### Topic Recap
Configuration files hold global project options, trace settings, and timeouts.

---

## Session Closing

### Session Summary
* We automated page object setups using custom Playwright fixtures.
* We scheduled test lifecycle checkpoints using setup and cleanup hooks.
* We optimized suite run times using Shared Authentication storage states.
* We parameterized test runs using external JSON data files.
* We decoupled endpoints using environment configurations and global settings.

### Knowledge Check Questions
1. *"What happens after the use keyword in custom fixtures? (Answer: Teardown runs)."*
2. *"Which hook runs once per test suite file? (Answer: beforeAll/afterAll)."*
3. *"Where do we save login cookies in Playwright? (Answer: Storage state JSON file)."*
4. *"How do you load dynamic env variables in NodeJS? (Answer: process.env)."*
5. *"What is the default test timeout in Playwright? (Answer: 30 seconds)."*

### Homework Guidance
Instruct students to complete the `Day_14` assignments:
1. Custom `loginPage` fixture inside `vjti-fixtures.ts`.
2. Parameterized login verification loading `student-credentials.json`.
3. Dynamic environment configuration mappings inside `playwright.config.ts`.

### Next Session Preview
In Day 15, we will explore advanced framework configurations, parallel executions, test groups/tags, custom reporters, and trace debugging.
