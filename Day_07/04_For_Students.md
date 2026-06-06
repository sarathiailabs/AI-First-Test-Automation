# Day 7: Playwright Fundamentals + Locators – Student Revision Notes

Quick reference guide to revise the concepts covered in the Playwright Fundamentals + Locators session. Use this to review definitions, common pitfalls, and locator priorities.

---

## Introduction to Playwright

### Definition
**Playwright** is an open-source testing framework developed by Microsoft that automates Chromium, WebKit, and Firefox engines using a single API. *(Microsoft dwara banaya gaya cross-browser automation testing framework).*

### Example
```javascript
const { chromium } = require('playwright');
```

### Key Points
* Runs tests on all major browsers (Chrome, Edge, Safari, Firefox).
* Features built-in auto-waiting for UI actions.
* Fast, stable, and completely open-source.

### Common Mistakes
* **Expecting legacy Selenium behaviors:** Playwright is not built on WebDriver and handles waiting and browser contexts completely differently.

---

## Playwright Architecture

### Definition
**Playwright Architecture** uses a WebSocket network connection to communicate directly with browser binaries via the Chrome DevTools Protocol (CDP), avoiding HTTP command translations. *(WebSocket-based direct browser engine connection jisse tests fast execute hote hain).*

### Example
A single command runs directly to Chromium over a WebSocket tunnel rather than starting separate executable drivers.

### Key Points
* Commits browser controls instantly in sub-milliseconds.
* Connects directly to browser engines, bypassing HTTP translation layers.
* Allows rich features like network interception and context isolation.

### Common Mistakes
* **Manually managing ChromeDriver binaries:** Playwright manages browser binaries natively; you do not need to install local chromedriver executables.

---

## Installation & Setup

### Definition
**Installation & Setup** is the command-line execution that downloads npm packages and sets up test folder templates. *(Command line tool jo testing framework ko configure karta hai).*

### Important Syntax
```bash
npm init playwright@latest
```

### Example
Running the initializer in terminal to set up a new project directory.

### Key Points
* Downloads Playwright test runner libraries.
* Automatically downloads WebKit, Chromium, and Firefox engines.
* Generates sample configurations and example scripts.

### Common Mistakes
* **Forgetting to install browsers on CI/CD pipelines:** Cloned projects need `npx playwright install` after `npm install` to download browser engines.

---

## Project Structure

### Definition
**Project Structure** is the standard file hierarchy generated during setup that divides tests, configs, and reports. *(Playwright ka standard folder layout).*

### Example
```text
tests/                 (Tests directory)
playwright.config.ts   (Master config)
package.json           (Dependencies)
```

### Key Points
* Core configuration resides in `playwright.config.ts`.
* Tests belong inside the `tests/` folder (or customized path).
* Reports are outputted inside `playwright-report/` after runs.

### Common Mistakes
* **Renaming config files randomly:** Playwright expects `playwright.config.ts` or `playwright.config.js` in the root. Changing this without a CLI argument will break runs.

---

## Test Runner

### Definition
The **Playwright Test Runner** is the command-line executor used to run spec files, generate HTML reports, or load interactive test browsers. *(Playwright script files ko execute karne ka terminal utility).*

### Important Syntax
```bash
npx playwright test
npx playwright test --ui
npx playwright test --debug
```

### Example
Running `npx playwright test --headed` to visually see tests run.

### Key Points
* Runs headlessly (in the background) by default.
* `--ui` launches an interactive dashboard for time-travel debugging.
* `--debug` runs tests line-by-line using a developer inspector.

### Common Mistakes
* **Using long `page.pause()` blocks in CI/CD runs:** The inspector window will hang forever in headless CI pipelines. Never commit debug scripts.

---

## Browser Contexts

### Definition
A **Browser Context** is a lightweight, isolated incognito-style session created within a single browser instance. *(Browser instance ke andar chalne wala isolated tab group session).*

### Important Syntax
```javascript
const context = await browser.newContext();
```

### Example
```javascript
const browser = await chromium.launch();
const context = await browser.newContext(); // Isolated incognito session
const page = await context.newPage();
```

### Key Points
* Complete isolation of cookies, localStorage, and caches between contexts.
* Spins up in milliseconds, making parallel test suites fast.
* Allows you to run multi-user tests concurrently.

### Common Mistakes
* **Sharing context states across tests accidentally:** Always run each test in its own context to ensure test isolation.

---

## Configuration Management

### Definition
**Configuration Management** is the modification of the master `playwright.config.ts` file to customize global parameters like base URLs, timeouts, viewport sizes, or screenshot settings. *(Timeouts aur config settings customize karne ka master brain file).*

### Example
```typescript
export default defineConfig({
  timeout: 30000,
  use: { headless: true, baseURL: 'https://vjti.ac.in' }
});
```

### Key Points
* Centralizes control over browser options.
* Defines browser profiles (projects).
* Sets retry counts for flaky tests.

### Common Mistakes
* **Hardcoding environment URLs in test code:** Use `baseURL` in the config file and reference relative paths like `await page.goto('/login')` in scripts.

---

## Multi-browser Execution

### Definition
**Multi-browser Execution** is the process of running the same test spec files simultaneously across Chromium, Firefox, and WebKit browser setups. *(Same tests ko multiple browsers par coordinate karke run karna).*

### Important Syntax
```bash
npx playwright test --project="firefox"
```

### Example
Running automated tests on Safari (WebKit) and Chrome (Chromium) in a single run.

### Key Points
* Configured using projects inside `playwright.config.ts`.
* Tests verify layout consistency across different engines in parallel.

### Common Mistakes
* **Assuming Chrome and Chromium behave differently:** Edge, Chrome, and Opera run on the same Chromium rendering engine. Test WebKit and Firefox to catch true cross-browser engine bugs.

---

## CSS Selectors in Playwright

### Definition
**CSS Selectors** locate elements by matching their HTML tag names, classes, IDs, or structural hierarchy. *(HTML class aur ID styling patterns se element target karna).*

### Important Syntax
```javascript
page.locator('input#student-roll')
```

### Example
```javascript
const inputField = page.locator('div.form-group > input.text-input');
```

### Key Points
* `#id` matches elements with `id="..."`.
* `.class` matches elements with `class="..."`.
* Tag matching (e.g. `input`) targets element node types.

### Common Mistakes
* **Using auto-generated class names:** CSS class names like `.MuiButton-root` or `.sc-bdVaJa` are dynamic and change on every webpack build. Avoid them.

---

## XPath in Playwright

### Definition
**XPath** is a XML query path language used to locate elements by traversing the HTML DOM tree structure vertically or horizontally. *(DOM tree traversal query language jo relative searches support karti hai).*

### Important Syntax
```javascript
page.locator("//input[@name='rollNo']")
```

### Example
```javascript
const parentRow = page.locator("//span[text()='Amul Butter']/ancestor::div[1]");
```

### Key Points
* Absolute XPaths (starting with `/html`) are highly fragile.
* Relative XPaths (starting with `//`) search matching nodes anywhere.
* XPath can traverse upwards using parent syntax (`..` or `ancestor`).

### Common Mistakes
* **Using long absolute paths:** Avoid path structures like `/html/body/div[1]/div[2]/form/input`. Use stable attributes instead.

---

## Locators: getByRole()

### Definition
`getByRole()` is an accessibility-focused selector that targets elements based on their semantic ARIA roles (button, combobox, textbox) and accessible names. *(ARIA roles aur visible labels se elements target karna).*

### Important Syntax
```javascript
page.getByRole('button', { name: 'Submit' })
```

### Example
```javascript
const payBtn = page.getByRole('button', { name: 'Pay Semester Fee' });
```

### Key Points
* It is Playwright's **recommended locator** method.
* Replicates how assistive screen-readers read elements for visually impaired users.
* Highly stable because semantic roles rarely change.

### Common Mistakes
* **Using the wrong role name:** A dropdown menu is `'combobox'`, not `'dropdown'`. Refer to ARIA role sheets when writing locators.

---

## Locators: getByText()

### Definition
`getByText()` locates elements by matching the visible text node strings rendered on the browser page. *(Visible screen text se node locate karna).*

### Important Syntax
```javascript
page.getByText('Error: Invalid Roll Number')
```

### Example
```javascript
const successBanner = page.getByText('Application Submitted Successfully', { exact: true });
```

### Key Points
* Matches substrings by default (case-insensitive).
* Pass `{ exact: true }` for exact text checks.
* Useful for verifying headers, validation messages, and paragraphs.

### Common Mistakes
* **Using dynamic text for selectors:** If text values change dynamically (e.g., date stamps or ticket prices), your tests will fail. Use test IDs or roles instead.

---

## Locators: getByLabel()

### Definition
`getByLabel()` targets form inputs by matching the text of their corresponding `<label>` element. *(Label tags se matching inputs target karna).*

### Important Syntax
```javascript
page.getByLabel('Roll Number')
```

### Example
```javascript
// Matches <input id="student-roll"> which has <label for="student-roll">Roll Number</label>
const rollNoField = page.getByLabel('Roll Number');
```

### Key Points
* Replicates how real users locate input boxes by reading the label beside them.
* Automatically couples inputs with labels, enhancing accessibility testing.

### Common Mistakes
* **Using labels with incomplete markup:** If developers forget to add the `for` attribute connecting the `<label>` to the `<input>` tag ID, `getByLabel` will fail to find it.

---

## Locators: getByPlaceholder()

### Definition
`getByPlaceholder()` targets input fields by matching their default placeholder prompt text attributes. *(Input placeholder content se target karna).*

### Important Syntax
```javascript
page.getByPlaceholder('Enter VJTI password')
```

### Example
```javascript
const passField = page.getByPlaceholder('Enter VJTI password');
```

### Key Points
* Directly matches the `placeholder="..."` string.
* Highly user-centric; tests what the user sees inside empty forms.

### Common Mistakes
* **Assuming placeholders remain static:** Designers often update placeholder text during copywriting passes.

---

## Locators: getByTestId()

### Definition
`getByTestId()` targets elements using a custom, QA-dedicated attribute (like `data-testid`) injected in code solely for testing stability. *(QA automation ke liye standard data-testid attribute target karna).*

### Important Syntax
```javascript
page.getByTestId('submit-form-btn')
```

### Example
```javascript
const submitBtn = page.getByTestId('submit-form-btn');
```

### Key Points
* Decouples testing from layout structure and styling classes entirely.
* It is the most resilient selector strategy for web automation.
* Uses the attribute `data-testid` by default (can be customized in configuration).

### Common Mistakes
* **Using test IDs for everything:** Use test IDs when accessibility locators (`getByRole`, `getByLabel`) are not sufficient or when elements change dynamically.

---

## Dynamic Locators

### Definition
**Dynamic Locators** are selectors built at runtime by combining text patterns or variables to match elements that change values on page load. *(Runtime variables se dynamic selectors banana).*

### Important Syntax
```javascript
page.locator(`tr#student-${studentId}`)
```

### Example
```javascript
const studentId = 1005;
const row = page.locator(`tr#student-${studentId} >> button`);
```

### Key Points
* Crucial for targeting specific rows in lists, tables, or product catalog cards.
* Use template literals to construct selectors.

### Common Mistakes
* **Using hardcoded values for dynamic elements:** If your test checks a row with a static index (e.g. `tr:nth-child(1)`), it will click the wrong item if the list order changes.

---

## Locator Chaining

### Definition
**Locator Chaining** joins multiple locators together in a chain, limiting the search scope of subsequent queries to the parent locator's DOM element boundaries. *(Parent selector ke scope me target locator search karna).*

### Important Syntax
```javascript
page.locator('.parent').locator('.child')
```

### Example
```javascript
const subResetBtn = page.locator('.hostel-card').getByRole('button', { name: 'Reset' });
```

### Key Points
* Narrows search scopes down.
* Avoids matching duplicate buttons or inputs across the page.

### Common Mistakes
* **Creating excessively long locator chains:** Chaining too many elements (e.g. `page.locator('.app').locator('.card').locator('.body').locator('button')`) makes selectors fragile. Keep it to parent -> child.

---

## Shadow DOM

### Definition
**Shadow DOM** is a web standard that isolates HTML styles and DOM structures inside encapsulated components. Playwright automatically pierces these root boundaries by default. *(Isolated Shadow DOM components me automatic entry).*

### Example
```javascript
// Playwright pierces Shadow DOM automatically
const blockACheck = page.locator('#block-a');
```

### Key Points
* You do not need special APIs to interact with elements inside a shadow root.
* Saves time compared to Selenium, which requires complex JS query structures.

### Common Mistakes
* **Writing legacy shadow host extraction scripts:** Do not write verbose shadow-root extraction JS code; query the child element id/role directly in Playwright.

---

## Accessibility Locators

### Definition
**Accessibility Locators** are selectors that query accessibility properties (like ARIA roles, labels, and descriptions) to ensure the application is screen-reader friendly. *(Accessibility roles aur values se select karna).*

### Important Syntax
```javascript
page.getByRole('button', { name: 'Close Alert' })
```

### Example
```javascript
const button = page.getByRole('button', { name: 'Close Alert' }); // targets aria-label="Close Alert"
```

### Key Points
* Encourages developers to build semantic HTML layouts.
* Prevents locators from breaking when visual styling is changed.

### Common Mistakes
* **Targeting elements using labels that screen-readers cannot see:** Ensure text is visible or ARIA attributes are valid.

---

## Locator Best Practices

### Definition
**Locator Best Practices** are the design principles for writing stable, maintainable element selectors in UI automation. *(Stable aur clean selectors setup karne ke rules).*

### Example
```javascript
// GOOD (Resilient)
page.getByTestId('login-btn')

// BAD (Fragile)
page.locator('div.container > div.row > form > button')
```

### Key Points
* Order of preference: `getByTestId()` -> `getByRole()` / `getByLabel()` / `getByPlaceholder()` -> CSS/XPath.
* Never use absolute XPaths.
* Decouple selectors from CSS styling classes.

### Common Mistakes
* **Using auto-generated XPaths from Chrome DevTools:** Avoid right-clicking an element and choosing "Copy XPath", as this generates absolute paths that break easily.

---

## Assignment Summary

* **Assignment 1: Launch Browser & Validate Page Title**
  * *Concepts Practiced:* Opening chromium contexts, navigating to login pages, and validating page titles.
* **Assignment 2: UI Form Locators Challenge (15 Locator Examples)**
  * *Concepts Practiced:* Writing roles, labels, placeholder selectors, XPath, and Shadow DOM selectors on complex form targets.
* **Assignment 3: Dynamic Kirana Cart & Chained Locators**
  * *Concepts Practiced:* Creating filter scopes, locator chaining, and verifying dynamic element removals.

---

## Quick Revision Sheet

| Selector Method | Scope | Best Usage Scenario | Example |
| :--- | :--- | :--- | :--- |
| **`getByTestId()`** | Custom test ID | Decoupled, production-ready tests | `page.getByTestId('save-btn')` |
| **`getByRole()`** | ARIA role + label | Buttons, links, headings, checkboxes | `page.getByRole('button', { name: 'Reset' })` |
| **`getByLabel()`** | Linked Label Text | Form input text boxes | `page.getByLabel('Username')` |
| **`getByPlaceholder()`** | Prompt placeholder text | Search inputs, login boxes | `page.getByPlaceholder('Enter Password')` |
| **`getByText()`** | Inner HTML text | Error messages, paragraphs, alerts | `page.getByText('Logins Failed')` |
| **`locator()`** | CSS or XPath | Decorative elements, parent-child flows | `page.locator('div.cart-row')` |

---

## Important Takeaways

1. **Isolation is Key:** Using separate Browser Contexts for each test ensures that sessions (like user log-ins) do not overlap, keeping tests independent and reliable.
2. **Accessible Testing:** Prioritizing `getByRole()` and `getByLabel()` selectors helps verify that your application is accessible to all users.
3. **Resilient Selectors:** Decoupling tests from layout hierarchies and styling classes (by using test IDs or user-facing labels) prevents test suites from breaking during frontend redesigns.
