# Day 13: Framework Design

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Framework Design Principles | 15 mins |
| Page Object Model (POM) | 20 mins |
| Component Object Model | 15 mins |
| Base Classes | 15 mins |
| Utilities | 15 mins |
| Reusable Components | 20 mins |
| Error Handling | 20 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Identify core Framework Design Principles (DRY, Single Responsibility, Maintainability).
* Implement the Page Object Model (POM) pattern to separate UI locators from test logic.
* Organize complex UI interfaces using the Component Object Model pattern.
* Design robust Base Classes (`BasePage`) to share core browser logic.
* Build external custom utility helper classes for test suite tasks.
* Create and import Reusable Components to avoid code duplication.
* Write structured Try-Catch blocks and capture diagnostic failure traces for Error Handling.

---

## Framework Design Principles

### Definition
**Framework Design Principles** are guidelines (such as DRY, Single Responsibility, and abstraction) that software testers follow to build structured, maintainable, and scalable test automation code bases. *(Framework design principles aise guidelines hain jinhe follow karke hum clean, reusable aur easy-to-maintain automation code likhte hain).*

### Key Concepts
* **DRY (Don't Repeat Yourself):** Avoid copying and pasting code. If a locator or method is used more than once, extract it into a class or function.
* **Single Responsibility Principle (SRP):** Each class or function should do exactly one thing. A login page class should handle login operations only, not invoice checks.
* **Separation of Concerns (SoC):** Separate your locator storage, test flow steps, configurations, and test data into different files.
* **Maintainability:** Ensure that if a UI developer changes a button class in the future, you only have to update it in one file, not 50 different test files.

### Visual Explanation
**The Kitchen Cabinet Analogy:**
Imagine a chaotic kitchen where spoons, spices, flour, and soap are all thrown into one large box. When you need a spoon, you have to dig through everything.
A well-designed framework is like a modern modular kitchen:
* **Spices Cabinet** $\rightarrow$ Locator variables.
* **Recipe book** $\rightarrow$ Test scripts.
* **Cooking utensils** $\rightarrow$ Helper methods.
Everything has its own place, making it easy to replace items or find them quickly.

### Example
#### Code
```javascript
// BAD PRACTICE: Violating DRY and SRP (Hardcoding locators inside test scripts)
test('Direct login test', async ({ page }) => {
  await page.goto('https://api.vjti.edu/login');
  await page.locator('#username-input').fill('student1');
  await page.locator('#password-input').fill('pass123');
  await page.locator('.submit-btn-vjti').click();
  await expect(page.locator('.welcome-msg')).toHaveText('Welcome Student');
});
```

### Real World Usage
When QA teams automate test cases for large enterprise systems (like IRCTC or Amazon), they use design principles to prevent their test suites from breaking every time a button's HTML structure changes.

### Topic Summary
Framework design principles advocate for code modularity, decoupling locator files from test logic to ensure clean project scalability.

---

## Page Object Model (POM)

### Definition
**Page Object Model (POM)** is a design pattern in test automation where each web page of the application is represented as a class file containing its locators and action methods, shielding test scripts from raw HTML changes. *(Page Object Model ek design pattern hai jahan web application ke har page ke liye ek class file banayi jaati hai jisme us page ke locators aur actions functions store hote hain).*

### Key Concepts
* **Page Class:** Defines page locators (constructor) and action functions (methods).
* **Test Script:** Imports the Page Class, instantiates it, calls actions, and performs assertions.
* **No Assertions in Page Classes:** Page classes should define *actions* (e.g. `fillLogin()`, `submit()`). The actual assertions (`expect()`) belong in the test script.

### Visual Explanation
```text
  +------------------------------------+
  |      TEST SCRIPT (vjti.spec.ts)    | ──► Calls methods, performs assertions
  +------------------------------------+
                   │ (Instantiates)
                   ▼
  +------------------------------------+
  |   PAGE CLASS (LoginPage.ts)        | ──► Encapsulates constructor locators,
  +------------------------------------+     action methods (fill, click)
```

### Example
#### Page Class
```typescript
import { Page, Locator } from '@playwright/test';

export class LoginPage {
  readonly page: Page;
  readonly usernameInput: Locator;
  readonly passwordInput: Locator;
  readonly loginButton: Locator;

  constructor(page: Page) {
    this.page = page;
    this.usernameInput = page.locator('#username-input');
    this.passwordInput = page.locator('#password-input');
    this.loginButton = page.locator('.submit-btn-vjti');
  }

  async navigate() {
    await this.page.goto('https://api.vjti.edu/login');
  }

  async login(username: string, password: string) {
    await this.usernameInput.fill(username);
    await this.passwordInput.fill(password);
    await this.loginButton.click();
  }
}
```
#### Test Script
```typescript
import { test, expect } from '@playwright/test';
import { LoginPage } from './LoginPage';

test('POM login verification', async ({ page }) => {
  const loginPage = new LoginPage(page);
  await loginPage.navigate();
  await loginPage.login('student1', 'pass123');

  // Assertion stays inside the test script
  await expect(page).toHaveURL(/.*dashboard/);
});
```

### Common Mistakes
* **Writing assertions inside POM methods:** Putting `expect(this.usernameInput).toBeVisible()` directly inside a page action. This couples verification logic with page structures, violating POM design rules. Keep assertions in the test script.

### Topic Summary
POM encapsulates page elements and operations within dedicated classes, ensuring test files interact only with clean page methods.

---

## Component Object Model

### Definition
The **Component Object Model** is an extension of POM where reusable sections of a page (like a Header, Sidebar, Navbar, or Footer) are modeled as independent component classes to avoid duplicate element definitions across multiple page models. *(Component Object Model me web page ke reusable parts (jaise header, navbar, widgets) ko alag component classes me divide kiya jata hai taaki unhe multiple pages me reuse kiya sake).*

### Key Concepts
* **Header / Footer reuse:** Instead of redefining the "Logout" button locator in 10 different Page classes, define it once in a `HeaderComponent` class.
* **Component nesting:** Page classes can contain instances of Component classes as properties.

### Visual Explanation
**The VJTI Student Portal Layout:**
```text
  +-------------------------------------------------------------+
  |                   [ HEADER COMPONENT ]                      |
  |             (Logo, Student Profile, Logout)                 |
  +----------------------+--------------------------------------+
  | [ SIDEBAR COMPONENT ]|           MAIN CONTENT PAGE          |
  | (Home, Fees, Grades) |         (Fee Receipt Form / Grid)    |
  +----------------------+--------------------------------------+
```

### Example
#### Component Class
```typescript
import { Page, Locator } from '@playwright/test';

export class HeaderComponent {
  readonly page: Page;
  readonly profileIcon: Locator;
  readonly logoutButton: Locator;

  constructor(page: Page) {
    this.page = page;
    this.profileIcon = page.locator('#profile-avatar');
    this.logoutButton = page.locator('button:has-text("Logout")');
  }

  async logout() {
    await this.profileIcon.click();
    await this.logoutButton.click();
  }
}
```
#### Page Class nesting the Component
```typescript
import { Page } from '@playwright/test';
import { HeaderComponent } from './HeaderComponent';

export class DashboardPage {
  readonly page: Page;
  readonly header: HeaderComponent; // Nesting component

  constructor(page: Page) {
    this.page = page;
    this.header = new HeaderComponent(page);
  }
}
```

### Topic Summary
Component Object Model splits layouts into reusable widgets nested inside page classes, reducing DOM locator duplication.

---

## Base Classes

### Definition
A **Base Class** (typically `BasePage`) is a parent page class containing common locators, utility properties, and reusable methods that are inherited by all child Page classes in the framework. *(Base Class ek parent class hoti hai jisme navigation ya element waits ke common methods hote hain jise baki saare child page classes extend (inherit) karte hain).*

### Key Concepts
* **Inheritance:** Child classes use the `extends` keyword to inherit properties and methods from `BasePage`.
* **Common actions:** Place functions like `waitForPageLoad()`, `navigateToPath()`, or global footer validations in the base class.

### Example
#### Base Class
```typescript
import { Page, Locator } from '@playwright/test';

export class BasePage {
  readonly page: Page;
  readonly logo: Locator;

  constructor(page: Page) {
    this.page = page;
    this.logo = page.locator('.vjti-header-logo');
  }

  async navigateTo(path: string) {
    await this.page.goto(`https://api.vjti.edu${path}`);
  }

  async waitForLoaderToDisappear() {
    await this.page.locator('.spinner-loader').waitFor({ state: 'detached' });
  }
}
```
#### Child Page Class
```typescript
import { BasePage } from './BasePage';
import { Page } from '@playwright/test';

export class FeesPage extends BasePage {
  constructor(page: Page) {
    super(page); // Calls parent BasePage constructor
  }

  async navigateToFeesSection() {
    await this.navigateTo('/student/fees'); // Inherited from BasePage
    await this.waitForLoaderToDisappear(); // Inherited from BasePage
  }
}
```

### Topic Summary
Base classes use OOP inheritance to bundle shared actions like loader waits and URL navigations, reducing boilerplate code in child pages.

---

## Utilities

### Definition
**Utilities** are helper classes or files containing static helper functions (such as date formatting, Excel/JSON reading, database queries, or string generators) that are independent of browser page logic. *(Utilities files me functions hote hain jo data processing ya calculation me help karte hain aur page page navigation se independent hote hain).*

### Key Concepts
* **Static Functions:** Direct helper executions (e.g. `DateTimeUtils.getFutureDate()`).
* **Environment Agnostic:** Do not require Playwright's `Page` object to run.

### Example
#### Utility File
```typescript
export class DateUtility {
  static getFormattedDate(): string {
    const date = new Date();
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    return `${year}-${month}-${day}`; // Returns YYYY-MM-DD
  }
}
```
#### Usage inside test script
```typescript
import { test } from '@playwright/test';
import { DateUtility } from './DateUtility';

test('Util verify receipt date', async ({ page }) => {
  const currentDate = DateUtility.getFormattedDate();
  console.log(`Verifying entry date: ${currentDate}`);
  // Use variable directly in inputs
});
```

### Topic Summary
Utilities house helper routines (like string parsing, database calls, or date math) decoupled from DOM locators.

---

## Reusable Components

### Definition
A **Reusable Component** represents a custom wrapper class for standard UI widgets (like a custom dropdown, date picker grid, or modal box) that handles all locator interactions natively, allowing tests to manipulate the widget using clean commands. *(Reusable Components custom objects hote hain jo browser widgets (jaise DatePicker calendar list) ko manage karte hain taaki complex UI steps bar-bar na likhne padein).*

### Key Concepts
* **Encapsulated Automation:** Custom widgets have nested locators (e.g. click input $\rightarrow$ select year $\rightarrow$ select day).
* **Parameters:** Custom action methods accept parameter payloads (e.g. `selectDate("2026-06-08")`).

### Example
#### Custom Dropdown Component
```typescript
import { Page, Locator } from '@playwright/test';

export class CustomDropdown {
  readonly page: Page;
  readonly trigger: Locator;
  readonly optionsList: Locator;

  constructor(page: Page, triggerSelector: string) {
    this.page = page;
    this.trigger = page.locator(triggerSelector);
    this.optionsList = page.locator('.custom-dropdown-options');
  }

  async selectOption(optionText: string) {
    await this.trigger.click(); // Click to open dropdown list
    await this.optionsList.locator('li').filter({ hasText: optionText }).click();
  }
}
```
#### Usage in Page Class
```typescript
import { Page } from '@playwright/test';
import { CustomDropdown } from './CustomDropdown';

export class StudentRegistrationPage {
  readonly page: Page;
  readonly branchDropdown: CustomDropdown;

  constructor(page: Page) {
    this.page = page;
    this.branchDropdown = new CustomDropdown(page, '#branch-select-box');
  }

  async register(branch: string) {
    await this.branchDropdown.selectOption(branch);
  }
}
```

### Topic Summary
Reusable components wrap complex HTML widget workflows into custom helper classes, making automation steps cleaner.

---

## Error Handling

### Definition
**Error Handling** is the implementation of structures (like Try-Catch-Finally statements) and diagnostic configs to handle execution exceptions gracefully, log meaningful failures, and run teardown operations safely. *(Error Handling codes me failures aur exceptions ko handle karne ka mechanism hai taaki script beech me crash na ho aur logs detailed milein).*

### Key Concepts
* **Try-Catch Block:** `try` runs test steps, `catch` captures errors, `finally` executes cleanups.
* **Custom Error Messages:** Throw readable descriptions rather than raw stack traces.
* **Playwright Screenshots/Traces:** Automatically capture screenshots or DOM state traces on failures.

### Example
#### Code
```typescript
import { test, expect } from '@playwright/test';

test('Verify student records with Error Handling', async ({ page }) => {
  await page.goto('https://api.vjti.edu/students');

  try {
    const list = page.locator('#student-records-list');
    // Set a short timeout to fail fast if resource is missing
    await expect(list).toBeVisible({ timeout: 5000 });
  } catch (error) {
    // Capture visual snapshot for debug diagnostics
    await page.screenshot({ path: `screenshots/failure-records.png` });
    
    // Throw descriptive custom error
    throw new Error(`CRITICAL: Student records list was not loaded on screen: ${error.message}`);
  }
});
```

### Common Mistakes
* **Using empty catch blocks:** Catching errors and doing nothing: `try { ... } catch(e) {}`. This hides failures, making tests pass even when UI components are completely broken. Always rethrow or log caught errors.

### Topic Summary
Error handling isolates failures, takes automated screenshots on crash events, and executes database rollback cleanup loops.

---

## Session Summary

### Key Takeaways
1. **Design Goals:** DRY and Single Responsibility keep code clean, and POM limits code breakage from HTML changes.
2. **Nesting Layouts:** Component Object Model abstracts headers and sidebars, nested into core page containers.
3. **Inheritance Paths:** Base classes bundle core actions (loader checks, redirects) to support child pages.
4. **Helper Isolation:** Utilities execute calculations, and custom components isolate multi-click widgets.
5. **Exception Handling:** Try-Catch captures crashes, logging custom diagnostics and saving screenshots.

### Important Interview Points
* **What is the difference between POM and Component Object Model?**
  * POM represents entire web pages (e.g. LoginPage class). The Component Object Model represents reusable sections inside pages (e.g. Header, Navbar). Component models are nested inside Page models to avoid duplicating locator strings.
* **Why should you keep assertions out of Page Object classes?**
  * Page classes model page elements and actions, not test validations. Putting assertions in POM files violates Single Responsibility, making it harder to reuse page methods in tests that expect different validation outcomes.
* **What operations belong inside a BasePage class?**
  * Shared tasks like base URL redirections, loader animations waiting, dialog handling, cookies verification, or global elements (like the organization logo).
* **How do you handle unexpected test crashes in frameworks?**
  * Using try-catch blocks to log trace information, calling page screenshot events in catch layers, and scheduling test retries in config files.

### Quick Revision Sheet

| Concept | File Goal | Playwright Code Pattern | Reusability Scope |
| --- | --- | --- | --- |
| **Base Class** | Shared base actions | `class LoginPage extends BasePage` | Global (across pages) |
| **Page Object**| Element Action layer | `loginPage.login(user, pass)` | Single page context |
| **Component** | Section layout | `dashboard.header.logout()` | Multi-page shared widgets |
| **Reusable widget**| Widget encapsulation | `dropdown.selectOption("Active")` | Framework-wide inputs |
| **Utility** | Math/Config help | `DateUtility.getFormattedDate()` | External helper script |
| **Error Handle**| Failure capture | `try { ... } catch (error) { ... }` | Diagnostic protection |
