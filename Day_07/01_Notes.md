# Day 7: Playwright Fundamentals + Locators

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Introduction to Playwright | 5 mins |
| Playwright Architecture | 10 mins |
| Installation & Setup | 10 mins |
| Project Structure | 5 mins |
| Test Runner Commands | 5 mins |
| Browser Contexts | 10 mins |
| Configuration Management | 5 mins |
| Multi-browser Execution | 5 mins |
| CSS Selectors in Playwright | 5 mins |
| XPath in Playwright | 5 mins |
| Locators: getByRole() | 5 mins |
| Locators: getByText() | 5 mins |
| Locators: getByLabel() | 5 mins |
| Locators: getByPlaceholder() | 5 mins |
| Locators: getByTestId() | 5 mins |
| Dynamic Locators | 10 mins |
| Locator Chaining | 5 mins |
| Shadow DOM Piercing | 5 mins |
| Accessibility Locators | 5 mins |
| Locator Best Practices | 5 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Explain the underlying WebSocket/CDP-based architecture of Playwright.
* Install and set up a fresh Playwright project from scratch.
* Customize settings in the `playwright.config.ts` configuration manager.
* Explaining the difference between Browsers, Browser Contexts, and Pages.
* Construct robust element locators using standard CSS, XPath, and accessibility-based role methods.
* Traverse dynamic and nested DOM components using locator chaining and shadow root piercing.

---

## Introduction

This module covers the core fundamentals of the Microsoft Playwright framework. We will study its modern node execution engine, inspect default project skeletons, and learn how to write resilient element locator strategies to build stable, flake-free automation suites.

---

## Introduction to Playwright

### Definition
**Playwright** is a modern, open-source library developed by Microsoft that enables reliable end-to-end testing for modern web applications across major browser engines. *(Playwright Microsoft dwara banaya gaya ek testing tool hai jo fast aur clean browser testing dynamic applications ke liye provide karta hai).*

### Key Concepts
* **Cross-Browser:** Runs tests on Chromium (Chrome, Edge), WebKit (Safari), and Gecko (Firefox).
* **Multi-Language:** Supports JS/TS, Python, Java, and C#.
* **Auto-Waiting:** Automatically waits for elements to be actionable (visible, enabled) before performing clicks or fills.
* **Resilience:** No sleep statements needed; directly reads browser state.

### Real World Usage
Replacing legacy Selenium architectures in organizations to speed up pipeline builds:
```javascript
const { chromium } = require('playwright');
```

### Common Mistakes
* **Thinking Playwright is just a Selenium wrapper:** Playwright is built completely from scratch using a new architecture and doesn't rely on WebDriver.

### Topic Summary
Playwright is a modern cross-browser web testing library featuring auto-waiting and browser engines isolation.

---

## Architecture

### Definition
**Playwright Architecture** utilizes a single connection via WebSocket protocols to communicate directly with browser developer endpoints (Chrome DevTools Protocol - CDP), bypassing middleman drivers. *(Playwright ka WebSocket network-level architecture jo direct browser engines ke control panel se communicate karta hai).*

### Key Concepts
* **Direct CDP Connection:** Communicates with the browser using Chrome DevTools Protocol over WebSockets.
* **No WebDriver Middleware:** Unlike Selenium which sends HTTP commands for every action, Playwright uses a single connection pipe.
* **Speed:** Since it is WebSocket-based, commands execute in sub-milliseconds, making tests extremely fast.

### Visual Explanation
Analogy: Remote-controlled toy car.
```text
  SELENIUM (HTTP Poll / Slow):
  [ Test Code ] ── HTTP HTTP ──► [ WebDriver Client ] ── HTTP ──► [ Browser Agent ]

  PLAYWRIGHT (WebSocket Protocol / Fast):
  [ Test Code ] ◄───────────────── Active WebSocket (CDP) ───────► [ Browser Engine ]
```

### Real World Usage
Allows real-time network interception, geo-location mocking, and console log listening:
```javascript
page.on('console', msg => console.log(`Browser console: ${msg.text()}`));
```

### Common Mistakes
* **Expecting standard WebDriver grid setup:** Playwright connects directly to browser binaries, avoiding local driver binaries maintenance.

### Topic Summary
Direct WebSocket communication with browser binaries makes Playwright faster and more robust than legacy WebDriver systems.

---

## Installation & Setup

### Definition
**Installation & Setup** is the command-line setup process that creates a fresh test suite scaffolding with browsers, sample tests, and configurations. *(Command-line dwara Playwright template setup karne ka command loop).*

### Key Concepts
* **Installer Command:** Run `npm init playwright@latest` inside your terminal folder.
* **Selections:** Choose between JavaScript/TypeScript, configure testing directories, and download browsers.
* **Package installation:** Installs `@playwright/test` library in `node_modules`.

### Syntax
```bash
npm init playwright@latest
```

### Example
#### Terminal Executions
```bash
npm init playwright@latest
# Choices:
# - TypeScript (Recommended)
# - Folder: tests
# - Add GitHub actions: Yes
# - Install Playwright browsers: Yes
```
#### Output
```text
✔ Success! Created a Playwright project at D:\Playwright_Automation_notes
```

### Real World Usage
Setting up clean repositories for fresh projects at your client environments.

### Common Mistakes
* **Skipping browser installation:** If you run `npm install` inside a cloned project but forget `npx playwright install`, your tests will throw a "Browsers not installed" crash error.

### Topic Summary
`npm init playwright@latest` installs node libraries and downloads browser binaries automatically.

---

## Project Structure

### Definition
**Project Structure** is the default folder structure generated during setup, organizing configuration, tests, and dependencies. *(Playwright dwara banaye gaye template files aur unki places).*

### Key Concepts
* **`playwright.config.ts`**: The main configuration center.
* **`tests/` folder**: Where test script files are placed (e.g. `login.spec.ts`).
* **`package.json`**: Node dependency tracking.
* **`playwright-report/`**: Folder where HTML reports are created after execution.

### Visual Explanation
```text
  D:\Playwright_Automation_notes
   ├── node_modules/          (Downloaded Libraries)
   ├── tests/                 (Your Test Files)
   │    └── login.spec.ts
   ├── playwright.config.ts   (Master Configuration)
   ├── package.json           (Dependencies)
   └── package-lock.json      (Version Lock)
```

### Topic Summary
Organizes test config files in the root, script specs inside `tests/`, and outputs reports in the report directory.

---

## Test Runner

### Definition
The **Playwright Test Runner** is the execution CLI tool used to compile, run tests, and open debug panels. *(Playwright tests ko run karne, UI check karne, aur debug karne ka tool).*

### Key Concepts
* **Standard run:** `npx playwright test` (Runs tests in headless mode).
* **Headed run:** `npx playwright test --headed` (Shows browser window).
* **UI Mode:** `npx playwright test --ui` (Interactive explorer with time travel).
* **Debug Mode:** `npx playwright test --debug` (Opens code step inspector).

### Syntax
```bash
# Run specific test file
npx playwright test tests/login.spec.ts

# Open UI dashboard
npx playwright test --ui
```

### Example
#### Run command
```bash
npx playwright test
```
#### Output
```text
Running 3 tests using 1 worker
  3 passed (4.2s)
To open last HTML report run:
  npx playwright show-report
```

### Topic Summary
The test runner executes tests locally or in CI environments, providing CLI, UI, and step-by-step debug panels.

---

## Browser Contexts

### Definition
A **Browser Context** is an isolated incognito-like session created inside a single browser instance, providing complete state isolation without relaunching browser binaries. *(Browser ke andar ek dynamic incognito tab session jo files/cookies ko isolate karta hai).*

### Key Concepts
* **Isolation:** Cookies, Local Storage, and Session states are 100% separate between contexts.
* **Lightweight:** Launching a new context takes milliseconds, whereas launching a new browser takes seconds.
* **Parallel execution:** Tests run concurrently in parallel contexts without interfering with each other's sessions.

### Visual Explanation
Analogy: Multi-desk computer lab.
```text
  [ SINGLE BROWSER BINARY RUNNING ]
      ├── [ Context A (Student 1 Session) ] ──► (No Cookies overlap)
      └── [ Context B (Student 2 Session) ] ──► (Isolated local storage)
```

### Example
#### Code
```javascript
const { chromium } = require('@playwright/test');

(async () => {
  const browser = await chromium.launch();
  
  // Creates isolated incognito session
  const context = await browser.newContext();
  const page = await context.newPage();
  
  await page.goto('https://erp.vjti.ac.in');
  await browser.close();
})();
```

### Common Mistakes
* **Re-launching browser for every test:** Avoid launching `chromium.launch()` inside every test block. Let Playwright manage contexts automatically to keep tests fast.

### Topic Summary
Browser Contexts provide cheap, fast, incognito-style session isolation, speeding up parallel test suites.

---

## Configuration Management

### Definition
**Configuration Management** is the customization of global run parameters (timeouts, viewport sizes, retry limits) using the `playwright.config.ts` configuration file. *(Global rules jo test speed, timeouts, and options set karti hain).*

### Key Concepts
* **`use` block:** Sets defaults like `baseURL`, `headless: true/false`, and `screenshot: 'only-on-failure'`.
* **`projects` block:** Defines browser configurations (Chromium, WebKit, Firefox).
* **Workers:** Sets how many parallel tests to run simultaneously (`workers: 4`).

### Example
#### Code
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './tests',
  timeout: 30000,
  workers: 2,
  use: {
    headless: false,
    viewport: { width: 1280, height: 720 },
    screenshot: 'only-on-failure',
  },
});
```

### Topic Summary
The config file is the master brain controls how, where, and on what devices your test suite executes.

---

## Multi-browser Execution

### Definition
**Multi-browser Execution** is the capability to run the same test scripts concurrently across Chromium, Firefox, and WebKit engines using configuration projects. *(Ek test script ko ek sath triple browser engines par chalana).*

### Key Concepts
* Configured in the `projects` section of the config file.
* Validates application functionality across engine versions in a single run.

### Example
#### Command
```bash
npx playwright test --project="chromium" --project="firefox"
```

### Topic Summary
Defines project configurations inside `playwright.config.ts` to automate cross-browser compatibility checks.

---

## CSS Selectors in Playwright

### Definition
**CSS Selectors** are patterns used to target HTML elements based on their tags, IDs, class names, or attributes. *(HTML elements ko unki design styling properties se locate karna).*

### Key Concepts
* **ID:** `#username`
* **Class:** `.form-input`
* **Attribute:** `input[name="rollNo"]`
* **Parent-Child:** `div.card > button`

### Example
#### Code
```javascript
// Locating the login input using CSS class and attribute combination
const loginField = page.locator('input.form-control[placeholder="Roll Number"]');
```

### Common Mistakes
* **Using long auto-generated CSS paths:** Auto-generated paths like `div > div > div > input` break instantly if the layout shifts slightly. Always target stable classes or attributes.

### Topic Summary
CSS selectors are widely used, but should rely on stable attributes rather than fragile structure layouts.

---

## XPath in Playwright

### Definition
**XPath** (XML Path Language) is a query language used to traverse the XML/HTML document structure, enabling upward traversal and text searches. *(XML structure me nodes ko find karne wala path system, jo backwards search allow karta hai).*

### Key Concepts
* **Absolute XPath:** Starts with `/html/body/div...` (Extremely fragile).
* **Relative XPath:** Starts with `//input[@id='username']` (Recommended).
* **Text Search:** `//button[text()='Submit']`
* **Upward Traversal:** Locate a child and find its parent via `..`.

### Syntax
```text
//tag[@attribute='value']
```

### Example
#### Code
```javascript
// Upward traversal: Find label whose container contains a specific class
const studentLabel = page.locator("//div[contains(@class, 'form-group')]//label");
```

### Common Mistakes
* **Using absolute paths:** Path values starting with `/html` break on every UI release. Use relative searches with text and stable attributes.

### Topic Summary
XPath is a powerful backup selector for dynamic layouts or backward/parent DOM traversals.

---

## Locators: getByRole()

### Definition
`getByRole()` is a modern accessibility-based locator method that finds elements by their semantic ARIA roles (button, link, heading) and names. *(Web elements ko unke screen reader role aur text name se identify karna).*

### Key Concepts
* **Role types:** button, link, heading, textbox, checkbox, dialog.
* **Name option:** Matches the visible button text or accessibility label text.
* **Interview Tip:** This is Playwright's **highly recommended** selector because it tests accessibility and mirrors real-world user interactions.

### Syntax
```javascript
page.getByRole('roleName', { name: 'Visible Text' })
```

### Example
#### Code
```javascript
// Target the submit button on the fees page
const submitBtn = page.getByRole('button', { name: 'Pay Semester Fee' });
```

### Topic Summary
`getByRole` targets elements based on semantic web standards, ensuring both stable test selectors and basic accessibility compliance.

---

## Locators: getByText()

### Definition
`getByText()` locates elements by their visible text content present inside the node. *(Screen par dikhne wale exact ya partial text se element target karna).*

### Key Concepts
* Finds static text fields, error notifications, or headings.
* Uses substring matching by default (case-insensitive).
* For exact matches, use option `{ exact: true }`.

### Example
#### Code
```javascript
// Assert the error notification displays correct text
const errorCard = page.getByText('Invalid Roll Number or Password', { exact: true });
```

### Topic Summary
`getByText` locates UI text nodes, labels, and toast messages easily using visible strings.

---

## Locators: getByLabel()

### Definition
`getByLabel()` locates form inputs by matching the text of their corresponding `<label>` tag element. *(Input fields ko unke side label name se target karna).*

### Key Concepts
* Looks for a `<label>` element with text matching your query.
* Inspects the `for` attribute on the label to connect to the target `<input>` tag's `id`.

### Example
#### HTML Source
```html
<label for="reg-roll">Student Roll Number</label>
<input id="reg-roll" type="text" />
```
#### Code
```javascript
// Focuses the linked input box automatically
const rollInput = page.getByLabel('Student Roll Number');
```

### Topic Summary
`getByLabel` targets form inputs naturally by mimicking how users read labels to fill text fields.

---

## Locators: getByPlaceholder()

### Definition
`getByPlaceholder()` targets inputs by matching the temporary prompt text displayed inside them before values are typed. *(Placeholder text se input boxes ko select karna).*

### Key Concepts
* Directly inspects the `placeholder="..."` attribute.
* Ideal for login screens, search inputs, and numeric fields.

### Example
#### Code
```javascript
// Find password field by placeholder prompt text
const passwordField = page.getByPlaceholder('Enter VJTI password');
```

### Topic Summary
`getByPlaceholder` locates text inputs using their placeholder prompt text.

---

## Locators: getByTestId()

### Definition
`getByTestId()` targets elements using a custom dedicated test attribute (default is `data-testid`) injected exclusively for testing. *(QA automation ke liye banaye gaye custom test IDs ko target karna).*

### Key Concepts
* **Industry Standard:** This is the most resilient locator strategy.
* Even if styling, class names, or layout labels change, the test script won't break because the `data-testid` attribute remains untouched.

### Syntax
In HTML:
```html
<button data-testid="portal-login-submit">Submit</button>
```
In Playwright:
```javascript
page.getByTestId('portal-login-submit')
```

### Example
#### Code
```javascript
// Resilient button click using test ID
await page.getByTestId('paytm-checkout-btn').click();
```

### Topic Summary
`getByTestId` is the gold standard locator strategy because it decouples test scripts from changing HTML classes and styles.

---

## Dynamic Locators

### Definition
**Dynamic Locators** are selectors constructed at runtime by integrating variables or text patterns to match elements that change on every reload. *(Runtime variable parameters add karke run hone wale dynamic locators).*

### Key Concepts
* Often used for items in tables, dynamic catalogs, or lists.
* Can use template string injection: `page.locator(\`button[data-id="${id}"]\`)`.

### Example
#### Code
```javascript
// Locate a specific student's edit button dynamically
const studentId = 1005;
const editButton = page.locator(`tr#student-${studentId} >> button.btn-edit`);
```

### Topic Summary
Dynamic locators handle changing IDs or runtime elements by constructing selector strings dynamically using variables.

---

## Locator Chaining

### Definition
**Locator Chaining** is the practice of combining multiple locators sequentially to narrow the search scope from a parent element to a child node. *(Ek element scope ke andar dusra selector search karna).*

### Key Concepts
* Narrows down the DOM search area.
* Prevents matching duplicate elements on the page.
* **Syntax:** `page.locator('parentSelector').locator('childSelector')`.

### Visual Explanation
Analogy: Finding a classroom, then finding the desk inside.
```text
  [ PAGE DOM ] ──► (Finds all buttons - too many!)
  
  [ PAGE ] >> locator("#fees-card") >> locator("button") ──► (Finds only button inside card!)
```

### Example
#### Code
```javascript
// Target the submit button only within the VJTI hostel form wrapper card
const hostelSubmit = page.locator('.hostel-card-body').getByRole('button', { name: 'Submit' });
```

### Topic Summary
Locator chaining narrows down scopes, preventing duplicate element matches by chaining selectors together.

---

## Shadow DOM Piercing

### Definition
**Shadow DOM Piercing** is Playwright's default behavior where it automatically searches inside encapsulated Shadow DOM roots without requiring special configuration APIs. *(Shadow DOM isolation components ke andar bina config commands ke automatic target karna).*

### Key Concepts
* **Encapsulation:** Shadow DOM isolates component HTML/CSS (used in Web Components).
* **Selenium Limitation:** Selenium requires executing complex JS scripts or locating the shadow host first.
* **Playwright Advantage:** Playwright locators automatically pierce shadow boundaries natively.

### Example
#### Code
```javascript
// Even if the input is isolated inside a custom Web Component Shadow DOM, Playwright finds it:
const shadowInput = page.locator('custom-input-field input');
```

### Topic Summary
Playwright automatically pierces Shadow DOM barriers, saving QAs from writing complex shadow root navigation code.

---

## Accessibility Locators

### Definition
**Accessibility Locators** are locator queries that locate elements using ARIA attributes (like `aria-label`, `aria-describedby`) to verify that screen reader support works correctly. *(Screen readers aur handicap support properties se elements locate karna).*

### Key Concepts
* Targets elements by accessibility properties.
* Ensures applications are usable by blind or visually impaired users.
* Matches standard testing philosophies: "If a user cannot find it, a test should not find it."

### Example
#### Code
```javascript
// Locate a close icon button that has no text but has an aria-label
const closeBtn = page.getByRole('button', { name: 'Close Alert' }); // targets aria-label="Close Alert"
```

### Topic Summary
Accessibility locators prioritize semantic HTML elements and ARIA labels, ensuring high usability and script reliability.

---

## Locator Best Practices

### Definition
**Locator Best Practices** are the structural design guidelines for selecting stable, maintainable element references in test scripts. *(Stability and readability ke liye best selectors choose karne ke rules).*

### Key Concepts
* **Priority Order:**
  1. `page.getByTestId()` (Dedicated tests IDs - completely decoupled).
  2. `page.getByRole()`, `page.getByLabel()`, `getByPlaceholder()` (Accessibility/user-facing).
  3. `page.getByText()` (Visible labels).
  4. Standard CSS selectors / XPaths (Avoid if possible).
* **Never use absolute XPaths** like `/html/body/div[1]/div[2]/input`.
* **Decouple from styling:** Do not use class names that change based on CSS design edits.

### Example
#### Good Selector:
```javascript
page.getByRole('button', { name: 'Submit Fees' })
```
#### Bad Selector (Fragile):
```javascript
page.locator('div.form-container > div.btn-row > button.btn-green-active')
```

### Topic Summary
Prioritize user-facing accessibility locators and test IDs over layout-dependent CSS/XPath selectors.

---

## Session Summary

### Key Takeaways
1. **CDP WebSocket Power:** Playwright's single WebSocket architecture connects directly to browser engines, making it fast and robust.
2. **Context Isolation:** Browser Contexts represent cheap, independent sessions (incognito) for parallel execution.
3. **User-Facing Priority:** Modern locator APIs like `getByRole` and `getByTestId` replace old fragile paths, aligning testing with accessibility standards.
4. **Auto-Piercing:** Playwright automatically searches Shadow DOMs, reducing configuration overhead.

### Important Interview Points
* **What is the difference between a Browser Context and a Page?**
  * A Browser represents the physical launched binary (e.g. Chrome). A Browser Context is an isolated incognito session inside that browser. A Page is a single tab running inside a context.
* **Why does Playwright recommend getByRole over custom XPaths?**
  * `getByRole` mimics how real users (and screen readers) perceive elements, ensuring tests assert both functionality and accessibility while remaining resistant to layout modifications.
* **Does Playwright support Shadow DOM?**
  * Yes, Playwright pierce Shadow DOM boundaries natively by default.

### Quick Revision Sheet

| Locator Method | Targets | Example | Best For |
| --- | --- | --- | --- |
| `getByTestId()` | `data-testid` attribute | `getByTestId('login-btn')` | Resilient, decoupled locators |
| `getByRole()` | ARIA role + name | `getByRole('button', {name:'Pay'})` | UI buttons, inputs, links |
| `getByLabel()` | Associated `<label>` text | `getByLabel('Roll Number')` | Text Inputs, Checkboxes |
| `getByPlaceholder()` | Input placeholder attribute | `getByPlaceholder('Search...')` | Search boxes, entry forms |
| `getByText()` | Text content | `getByText('Successful')` | Alerts, message checks |
