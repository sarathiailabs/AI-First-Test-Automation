# Day 7: Playwright Fundamentals + Locators – Trainer Teaching Guide

This guide is designed for instructors delivering the "Playwright Fundamentals + Locators" session. It details the lecture scripts, engagement strategies, whiteboard sketches, live terminal commands, and student coaching tips.

---

## Session Opening

### Welcome Script
"Hello everyone! Welcome to Day 7. In our last session, we mastered web fundamentals: DOM trees, HTML, CSS, and how servers talk to browsers. Today, we are taking the steering wheel. We are going to install Microsoft Playwright, explore its project layout, launch real browsers programmatically, and write element locator strategies. Let's get started."

### Session Goal
By the end of today's 2-hour session, you will be able to set up a new Playwright project, explain its WebSocket architecture, configure browser runs, and write stable, production-grade locators using user-centric methods (like `getByRole` and `getByTestId`). This is the foundation of writing tests that do not break.

### Motivation
Imagine writing an automation suite of 500 tests in Selenium. Every time you run it, 30 tests fail randomly because the network is slightly slow, or a loading button took 50 milliseconds longer to appear. You spend hours writing `Thread.sleep()` in your code, which slows down the build. Playwright solves this natively. It uses a lightning-fast direct socket connection and auto-waiting to make tests extremely fast and stable.

---

## 1. Introduction to Playwright

### Trainer Introduction
"What is Playwright? It is a modern, open-source testing tool developed by Microsoft. It is designed to automate modern single-page applications (built on React, Angular, Vue) across Chrome, Firefox, and Safari engines. The primary reason companies are migrating from Selenium to Playwright is speed, stability, and its modern development experience."

### Student Engagement Questions
1. How many of you have worked with Selenium before? What was your biggest challenge?
2. Why is a test that fails 'sometimes' (flaky test) worse than a test that fails 'always'?
3. Why do we need to test our web application on multiple browser engines?

### Whiteboard Teaching
Draw the feature highlights:
```text
  ┌────────────────────────────────────────────────────────┐
  │                   PLAYWRIGHT CORES                     │
  ├────────────────────────────────────────────────────────┤
  │  - Cross-Browser: Chromium, WebKit, Firefox            │
  │  - Auto-Waiting: Actionability checks before clicks    │
  │  - Multi-Language: TS/JS, Python, Java, C#             │
  │  - Native Tooling: Tracing, UI Mode, Codegen           │
  └────────────────────────────────────────────────────────┘
```

### Teaching Flow
1. Introduce Microsoft Playwright and its purpose.
2. Outline the core features: cross-browser, auto-waiting, tracing, codegen.
3. Discuss the difference between raw browser engines vs installed browsers.

### Live Coding Demonstration
#### Step 1
Open terminal and display the installed Node.js version (`node -v`). Show that Playwright requires Node.js v16+.
#### Step 2
Show the Playwright homepage (`playwright.dev`) and briefly point out the documentation tabs.

### Common Student Doubts
#### Student Question
"Is Playwright only for testing, or can I use it for web scraping?"
#### Trainer Response
"Although designed as a testing framework, because it can launch headed/headless browsers and interact with elements, many companies use it for high-speed web scraping and data extraction."

### Real World Discussion
Explain that modern fast-paced startups run test suites in CI pipelines on every single pull request. Playwright's high execution speed makes this model practical, whereas Selenium test runs took too long to run on every commit.

### Topic Recap
* **Definition:** A Microsoft-built library for cross-browser E2E testing.
* **Takeaway:** Playwright runs on Chromium, WebKit, and Firefox with native auto-waiting.

### Transition Script
"Now, let's look under the hood to see how Playwright achieves this extreme speed and stability: its WebSocket architecture."

---

## 2. Playwright Architecture

### Trainer Introduction
"Selenium uses a protocol where test commands are sent via HTTP requests to local driver executables, which translate them for the browser. This loop takes time. Playwright connects directly to the browser binaries using a persistent, active WebSocket connection. It is like having a direct phone line to the browser's engine."

### Student Engagement Questions
1. What is the difference between sending an HTTP request and opening a continuous WebSocket?
2. What is the Chrome DevTools Protocol (CDP)?
3. Why does direct network access make tests faster?

### Whiteboard Teaching
Draw the connection comparison:
```text
  Selenium Loop:
  [ Code ] ── HTTP ──► [ WebDriver Executable ] ── Driver API ──► [ Browser ]

  Playwright Connection:
  [ Node.js Test Runner ] ◄══════ WebSocket (CDP Protocol) ══════► [ Browser Engine ]
```

### Teaching Flow
1. Draw the Selenium HTTP polling model vs the Playwright WebSocket architecture.
2. Explain how CDP (Chrome DevTools Protocol) allows direct control over browser processes.
3. Discuss how this direct connection enables advanced controls like network mocking, console logs listening, and geolocation overrides.

### Live Coding Demonstration
#### Step 1
Write a small script that registers a console listener:
```javascript
page.on('console', msg => {
  console.log(`[BROWSER LOG]: ${msg.text()}`);
});
```
#### Step 2
Explain that this listener works instantly because Playwright is connected directly to the browser's console output stream via WebSockets.

### Common Student Doubts
#### Student Question
"Does Playwright support older browsers like Internet Explorer 11?"
#### Trainer Response
"No. Playwright targets modern web engines (Chromium, Firefox, WebKit). Internet Explorer is deprecated, so Playwright does not support it. For legacy enterprise IE testing, Selenium is still used."

### Interview Talking Points
* **Interview Question:** "Explain the architectural difference between Selenium and Playwright."
* **Terminology:** *Chrome DevTools Protocol (CDP)*, *WebSocket connection*, *Bidirectional communication*.

### Transition Script
"Let's see how easy it is to install this architecture and set up a new project."

---

## 3. Installation & Setup

### Trainer Introduction
"To install Playwright, you only need Node.js and a single command-line instruction. Playwright handles downloading the library, setting up the folders, and downloading secure, isolated browser binaries for Chromium, WebKit, and Firefox."

### Student Engagement Questions
1. Why does Playwright download its own custom browser binaries instead of using the Chrome application already installed on your PC?
2. What is `npm`?

### Whiteboard Teaching
List the setup steps:
```text
  1. Open terminal in project folder.
  2. Run: npm init playwright@latest
  3. Select Language: TypeScript (Recommended)
  4. Select tests directory: tests
  5. Select CI configuration: Yes/No
  6. Confirm browser downloads.
```

### Teaching Flow
1. Explain npm packages.
2. Introduce the `npm init playwright@latest` initializer command.
3. Walk through the interactive CLI options.
4. Explain the browser binary download phase.

### Live Coding Demonstration
#### Step 1
Create a new folder: `mkdir playwright-demo` and navigate inside it.
#### Step 2
Run the initializer command:
```bash
npm init playwright@latest
```
#### Step 3
Select the options interactively (TypeScript, tests folder, download browsers).
#### Step 4
Show the downloaded output logs on the terminal.

### Common Student Doubts
#### Student Question
"Why are browser downloads so large? Can I skip them?"
#### Trainer Response
"Playwright downloads full browser engines (around 100-150MB each) to ensure that tests run in clean, reproducible, and isolated environments. You can skip them using `--no-browsers`, but you won't be able to run tests locally until you run `npx playwright install`."

### Topic Recap
* **Command:** `npm init playwright@latest` installs node modules and launches browser downloads.

### Transition Script
"Now that installation is complete, let's explore the files and folders generated."

---

## 4. Project Structure

### Trainer Introduction
"When the installation finishes, you will see a specific set of files in your workspace. Let's look at the config file, the tests directory, and where package dependencies are tracked. Knowing this layout keeps our projects clean."

### Student Engagement Questions
1. Where should you save your test script files?
2. What is the role of `playwright.config.ts`?

### Whiteboard Teaching
Sketch the folder structure:
```text
  [ Project Folder ]
    ├── tests/                  <── Save your test scripts here (e.g. login.spec.ts)
    │    └── example.spec.ts
    ├── playwright.config.ts    <── Master configuration settings
    ├── package.json            <── Installed npm dependencies
    └── package-lock.json
```

### Teaching Flow
1. Open the project folder in VS Code.
2. Explain the purpose of `playwright.config.ts`.
3. Detail the `tests/` folder.
4. Point out the `package.json` dependency file.

### Live Coding Demonstration
#### Step 1
Open the newly created `playwright-demo` folder in VS Code.
#### Step 2
Open `package.json` and point out `@playwright/test` inside the `devDependencies` block.
#### Step 3
Open `playwright.config.ts` and briefly highlight the root configuration options.

### Topic Recap
* **Structure:** Test configurations live in the root config file; test specs are organized inside the `tests/` folder.

### Transition Script
"Now that we know where files live, let's run the default test suite using the Test Runner."

---

## 5. Test Runner Commands

### Trainer Introduction
"Playwright provides a built-in test runner. We can run tests in the background (headless), open a headed browser window, or launch an interactive UI mode that lets us inspect locator steps visually."

### Student Engagement Questions
1. What does 'headless' mean in browser automation?
2. Why is running tests in headless mode preferred for CI/CD servers?
3. What is the difference between `--headed` and `--ui`?

### Whiteboard Teaching
Write out the core CLI commands:
```text
  Run all tests (headless):      npx playwright test
  Run with browser visible:      npx playwright test --headed
  Run interactive UI mode:       npx playwright test --ui
  Debug code line-by-line:       npx playwright test --debug
```

### Teaching Flow
1. Define headed vs headless browser runs.
2. Introduce the core execution commands.
3. Demonstrate UI Mode time travel features.
4. Demonstrate the Debug inspector.

### Live Coding Demonstration
#### Step 1
Run the default example tests:
```bash
npx playwright test
```
#### Step 2
Open the default HTML report:
```bash
npx playwright show-report
```
#### Step 3
Run tests in UI mode:
```bash
npx playwright test --ui
```
Show students the interactive UI dashboard, select a test, click run, and show how they can step through actions.

### Common Student Doubts
#### Student Question
"My test runs fine in headed mode, but fails in headless mode. Why?"
#### Trainer Response
"Headless mode runs faster and doesn't paint layout pixels. Sometimes, elements that are off-screen or require styling calculations are not considered actionable in headless runs. To debug, run in UI mode or capture screenshots on failure."

### Transition Script
"Let's look at one of the most powerful features of Playwright's execution model: Browser Contexts."

---

## 6. Browser Contexts

### Trainer Introduction
"In Selenium, if you want to run 3 tests in parallel, you must launch 3 separate Chrome browser processes. This consumes a lot of CPU and RAM. Playwright launches the browser process once and creates separate incognito-like sessions called Browser Contexts. Each context runs independently with its own cookies and cache, ensuring isolation at minimal cost."

### Student Engagement Questions
1. What happens if you open two incognito tabs in Chrome and log into the same site with different user profiles? Do they interfere with each other?
2. Why is browser context creation faster than launching a new browser process?

### Whiteboard Teaching
Draw the isolation boundary:
```text
  [ SINGLE CHROMIUM PROCESS ]
      ├── [ Context 1 (Session: Student A) ] ──► Page (Tab 1)
      └── [ Context 2 (Session: Admin B) ]   ──► Page (Tab 2)
```
Explain that Context 1 cannot read the cookies of Context 2.

### Teaching Flow
1. Explain the cost of launching browser binaries.
2. Introduce the Browser Context abstraction.
3. Contrast it with launching multiple browser instances.
4. Explain how this enables fast parallel test execution.

### Live Coding Demonstration
#### Step 1
Write a script demonstrating manual context creation:
```javascript
const { chromium } = require('@playwright/test');
const browser = await chromium.launch({ headless: false });

const context1 = await browser.newContext();
const page1 = await context1.newPage();
await page1.goto('https://erp.vjti.ac.in');

const context2 = await browser.newContext();
const page2 = await context2.newPage();
await page2.goto('https://erp.vjti.ac.in');
```
#### Step 2
Run this script and show that they represent two isolated incognito sessions on the screen.

### Common Student Doubts
#### Student Question
"If I use standard `test('...', async ({ page }) => {})`, do I need to create contexts manually?"
#### Trainer Response
"No! Playwright's test runner automatically handles creating a new context and page fixture for each test behind the scenes, ensuring clean test isolation by default."

### Transition Script
"Let's see how we can configure these contexts globally using Configuration Management."

---

## 7. Configuration Management

### Trainer Introduction
"Instead of writing browser launch settings in every single script file, we define them in `playwright.config.ts`. Here, we can configure timeouts, specify headed/headless runs, set viewports, and enable automatic screenshot capturing."

### Student Engagement Questions
1. Why is it a bad practice to hardcode browser configuration settings in your test code?
2. What does a 30000ms timeout mean?

### Whiteboard Teaching
Outline the main config keys:
```typescript
  export default defineConfig({
    timeout: 30000,           // 30 seconds limit for each test
    workers: 2,               // Run 2 tests in parallel
    use: {
      headless: false,        // View browser actions
      screenshot: 'on',       // Save screenshots for every run
    }
  });
```

### Teaching Flow
1. Review the config options generated by default.
2. Explain the `use` block (headless, baseURL, viewport, screenshots, video).
3. Discuss timeout configurations.

### Live Coding Demonstration
#### Step 1
Open `playwright.config.ts` in VS Code.
#### Step 2
Change `headless: false` in the `use` section.
#### Step 3
Add `screenshot: 'only-on-failure'` to the configuration.
#### Step 4
Run a test to demonstrate that it now opens headed and saves failure screenshots automatically.

### Transition Script
"With configurations mastered, let's explore how we run these tests across multiple browser engines simultaneously."

---

## 8. Multi-browser Execution

### Trainer Introduction
"To verify that our web application works on Chrome, Edge, Firefox, and Safari, we configure Projects inside our config file. Playwright will run your tests across all configured engines in parallel."

### Student Engagement Questions
1. If a website works on Chrome, is it guaranteed to work on Safari? Why or why not?
2. What are 'Projects' in the Playwright config file?

### Whiteboard Teaching
Show the project array layout:
```typescript
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'firefox', use: { ...devices['Desktop Firefox'] } },
    { name: 'webkit', use: { ...devices['Desktop Safari'] } }
  ]
```

### Teaching Flow
1. Define cross-browser validation.
2. Explain project profiles in the config file.
3. Show how to run specific projects via CLI.

### Live Coding Demonstration
#### Step 1
Open the project section of `playwright.config.ts`.
#### Step 2
Run a command to execute tests only on the WebKit (Safari) engine:
```bash
npx playwright test --project="webkit"
```
#### Step 3
Show the test report verifying it executed on WebKit.

### Transition Script
"Now that we know how to configure and execute browser sessions, let's learn how to find elements inside our pages: starting with CSS and XPath selectors."

---

## 9. CSS Selectors & XPath

### Trainer Introduction
"To click a button or fill a form, Playwright must locate the target HTML element in the DOM tree. The two classic selector strategies are CSS Selectors and XPath. CSS locates elements by styling attributes, while XPath traverses the tree structure."

### Student Engagement Questions
1. What character is used to select a class in CSS? What character is used for an ID?
2. What is the difference between an absolute XPath and a relative XPath? Which is better for automation?

### Whiteboard Teaching
Write selector formats side-by-side:
```text
  Targeting <input id="student-roll" class="form-input" />
  
  CSS:
  - By ID:    #student-roll
  - By Class:  .form-input
  
  XPath:
  - Relative:  //input[@id='student-roll']
```

### Teaching Flow
1. Review HTML attributes: ID, Class, Type.
2. Write matching CSS selectors.
3. Write relative XPaths.
4. Explain why absolute XPaths should never be used.

### Live Coding Demonstration
#### Step 1
Open browser DevTools on any form page. Press `Ctrl + F` inside the Elements panel.
#### Step 2
Search using CSS selectors: `#student-roll`, `input[type="text"]`.
#### Step 3
Search using relative XPath: `//input[@id='student-roll']`.
#### Step 4
Show students how the element highlights on the screen as you type.

### Common Student Doubts
#### Student Question
"Should I copy XPaths directly from Chrome's inspect element menu?"
#### Trainer Response
"No! Right-clicking and selecting 'Copy XPath' often generates absolute paths like `/html/body/div[1]/div[2]/form/div[3]/input`. These break if a developer inserts a new div wrapper. Always write short, relative XPaths."

### Transition Script
"While CSS and XPath are powerful, Playwright recommends using user-centric, accessibility-based locators. Let's learn about these modern locators."

---

## 10. Playwright Modern Locators

### Trainer Introduction
"Instead of relying on fragile code elements like class names, Playwright introduces modern locator APIs that look for elements the way a real user does: by their role (like button or textbox), label, placeholder text, or custom test IDs. Let's explore these five methods."

### Student Engagement Questions
1. If you are a user looking at a login screen, how do you locate the password field? (By reading 'Password' label or placeholder).
2. What is the benefit of a custom test ID attribute (`data-testid`)?

### Whiteboard Teaching
List the five core modern locators:
```text
  1. page.getByRole('button', { name: 'Submit' })   <── Semantic Role
  2. page.getByLabel('Roll Number')                  <── Connected <label> text
  3. page.getByPlaceholder('Enter Roll Number')     <── Input prompt placeholder
  4. page.getByText('Login Failed')                  <── Visible text node
  5. page.getByTestId('submit-btn')                  <── QA data-testid attribute
```

### Teaching Flow
1. Explain why user-facing locators are stable and accessible.
2. Break down each locator method individually.
3. Discuss role properties and input types.
4. Explain how test IDs decouple tests from styling.

### Live Coding Demonstration
#### Step 1
Write a test block demonstrating form locators:
```typescript
test('VJTI Form Fill Demo', async ({ page }) => {
  await page.goto('https://erp.vjti.ac.in/portal/login');

  // Fill using placeholder
  await page.getByPlaceholder('Enter Roll Number').fill('1005');

  // Click using role
  await page.getByRole('button', { name: 'Submit' }).click();
});
```
#### Step 2
Explain how each line mimics standard user behaviors.

### Common Student Doubts
#### Student Question
"What happens if `getByRole` finds multiple buttons with the same name?"
#### Trainer Response
"Playwright will throw a 'strict mode violation' error stating that multiple elements matched. To resolve this, you must refine your search using locator chaining or filtration, which we will learn next."

### Transition Script
"Let's look at advanced locator concepts like dynamic targets, chaining, and shadow DOM piercing."

---

## 11. Advanced Locator Concepts

### Trainer Introduction
"Real-world DOMs are dynamic. Items inside carts change, structures are encapsulated inside Shadow DOMs, and elements can be deeply nested. To handle this, we use advanced locator techniques like locator chaining, dynamic variable injection, and shadow root piercing."

### Student Engagement Questions
1. How do you click a button inside a specific card if there are five cards with identical buttons?
2. What is the benefit of chaining locators?

### Whiteboard Teaching
Draw locator chaining scopes:
```text
  page.locator('.cart-item-row') ──► (Finds all item rows)
       .filter({ hasText: 'Amul Butter' }) ──► (Filters down to the Amul Butter row)
       .getByRole('button', { name: 'Delete' }) ──► (Finds the Delete button inside that row)
```

### Teaching Flow
1. Detail Locator Chaining (`page.locator().locator()`).
2. Show dynamic selectors with template strings.
3. Explain how Playwright automatically pierces Shadow DOM boundaries.
4. Review locator best practices and priority orders.

### Live Coding Demonstration
#### Step 1
Write a chained locator script for a cart layout:
```javascript
const itemRow = page.locator('.cart-item-row').filter({ hasText: 'Amul Butter' });
const deleteBtn = itemRow.getByRole('button', { name: 'Delete' });
await deleteBtn.click();
```
#### Step 2
Demonstrate that Playwright successfully targets only the second row button.
#### Step 3
Point out that we don't need any complex code to click checkbox selectors inside Web Component shadow hosts.

### Topic Recap
* **Rule:** Chain selectors to narrow scopes; filter by unique text when dealing with dynamic tables.
* **Practice:** Always target `getByTestId` or accessibility roles first.

---

## Session Closing

### Session Summary
"Today we covered:
1. Playwright Setup: Installing Node libraries and downloading browser engines.
2. CDP Architecture: Persistent WebSockets that make execution fast.
3. Browser Contexts: Isolated incognito-like sessions that speed up parallel tests.
4. Config Management: Setting timeouts, workers, and screenshots.
5. Locators: CSS, XPaths, and modern accessibility-based methods (`getByRole`, `getByTestId`).
6. Advanced Locators: Chaining, filtering, and automatic shadow root piercing."

### Knowledge Check Questions
1. What command initiates a new Playwright project? (`npm init playwright@latest`).
2. Which locator strategy is the most resilient to UI style changes? (`getByTestId()`).
3. Does Playwright require extra code to select elements inside Shadow DOMs? (No, it pierces them automatically).
4. What is the role argument value for a dropdown selector in `getByRole`? (`'combobox'`).
5. How does Playwright communicate with browser binaries? (Over WebSockets using CDP).

### Assignment Introduction
"To practice these concepts, open `02_Assignments.md`. You will write a test file (`vjti-title.spec.ts`) to validate a page title, construct 15 custom locator strategies for the VJTI Hostel Form, and build a chained filter locator for a dynamic Kirana cart. These exercises mirror common interview challenges."

### Homework Guidance
* Complete the three assignments.
* Try running your script in headed mode and UI mode (`--ui`).
* Inspect your favorite e-commerce cart page using DevTools and draft a chained locator for a specific item's quantity button.

### Next Session Preview
"In our next session, we will learn UI Actions and Assertions. We will programmatically write actions like click, fill, hover, drag-and-drop, and use assertions to verify page states."
