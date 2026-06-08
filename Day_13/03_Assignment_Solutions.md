# Day 13: Framework Design – Solutions

This file contains solutions for the theory questions and practical programming assignments assigned for Day 13.

---

## Theory Question Solutions

### Question 1
What is the Page Object Model (POM)? Explain how it improves the maintainability and readability of an automation test suite.

### Answer
* **POM:** A structural design pattern where page locators and actions are encapsulated within page-specific class files, separate from test scripts.
* **Maintainability Improvement:** If a developer alters a locator's ID (e.g. changing `#login-submit` to `.submit-login`), you only update it once in the constructor of the Page Object class. Without POM, you would have to search and update that locator in dozens of raw test scripts.
* **Readability Improvement:** Test scripts become clean and expressive, reading like human-friendly steps (e.g., `await loginPage.login('user', 'pass')`) instead of raw css commands.

---

### Question 2
Why is it considered a bad design pattern to put assertions (`expect()`) directly inside Page Object classes? Where should assertions reside?

### Answer
* **Single Responsibility Violation:** Page classes model elements and actions, not validations.
* **Reduced Reusability:** If `loginPage.login()` includes an assertion that checks for a successful login, you cannot reuse that same login method in negative test scenarios (like verifying that entering an invalid password triggers an error alert).
* **Where they reside:** All assertions should reside inside the test scripts (`.spec.ts` files) where the test logic is run and reported.

---

### Question 3
What is the difference between POM and the Component Object Model? How does nesting components inside page classes help reduce code duplication?

### Answer
* **Difference:** POM represents complete pages (e.g., `LoginPage`, `ProfilePage`), while the Component Object Model represents reusable visual structures nested inside pages (e.g., `Header`, `Navbar`, `Footer`).
* **Nesting Benefits:** A common header contains the "Logout" button. Instead of defining the Logout locator inside the `DashboardPage`, `GradesPage`, and `FeesPage` POM classes, it is defined once inside a `HeaderComponent` class and nested as a property in each page model, eliminating duplicate locators.

---

### Question 4
What type of locator declarations and action methods belong in a `BasePage` class? When should a page class *not* inherit from `BasePage`?

### Answer
* **Belongs in BasePage:** Global elements (e.g. organization logo, copyright footer) and common helper methods (e.g., navigating to relative URLs, waiting for loading spinners to disappear, handling alert modals).
* **When not to inherit:** A page class should not inherit from `BasePage` if the page operates on a completely different framework engine, has a distinct viewport context, or does not share any UI structure with the rest of the application (though in standard frameworks, inheriting from a unified base class is almost always preferred).

---

### Question 5
Explain how empty `catch` blocks in Try-Catch error handling can hide actual application bugs. What are the best practices for handling errors in automated scripts?

### Answer
* **Hiding Bugs:** If a test step throws an error (e.g. a button was not visible) but the code catch block is empty:
  ```typescript
  try { await button.click(); } catch(e) { /* empty */ }
  ```
  The execution continues to the next line. Playwright logs this test as "Passed" even though the button was never found or clicked.
* **Best Practices:** Always log the exception details, capture a screenshot for diagnostic reports (`await page.screenshot()`), and re-throw the error (`throw new Error(...)`) so the automation pipeline registers the failure.

---

## Programming Assignment Solutions

### Assignment 1: VJTI Login Page Object Model

#### Solution Code (`VJTI_LoginPage.ts`)
```typescript
import { Page, Locator } from '@playwright/test';

export class VJTI_LoginPage {
  readonly page: Page;
  readonly usernameInput: Locator;
  readonly pinInput: Locator;
  readonly submitButton: Locator;

  constructor(page: Page) {
    this.page = page;
    this.usernameInput = page.locator('#username');
    this.pinInput = page.locator('#student-pin');
    this.submitButton = page.locator('#login-submit');
  }

  async navigate() {
    await this.page.goto('https://api.vjti.edu/login');
  }

  async login(regNo: string, pin: string) {
    await this.usernameInput.fill(regNo);
    await this.pinInput.fill(pin);
    await this.submitButton.click();
  }
}
```

---

### Assignment 2: VJTI Dashboard Page & Reusable Navbar Component

#### Solution Code

##### Navbar Component (`NavbarComponent.ts`)
```typescript
import { Page, Locator } from '@playwright/test';

export class NavbarComponent {
  readonly page: Page;
  readonly userGreeting: Locator;
  readonly logoutButton: Locator;

  constructor(page: Page) {
    this.page = page;
    this.userGreeting = page.locator('.user-greeting');
    this.logoutButton = page.locator('#logout-btn');
  }

  async logout() {
    await this.logoutButton.click();
  }
}
```

##### Dashboard Page Class (`DashboardPage.ts`)
```typescript
import { Page, Locator } from '@playwright/test';
import { NavbarComponent } from './NavbarComponent';

export class DashboardPage {
  readonly page: Page;
  readonly navbar: NavbarComponent; // Nested Navbar component
  readonly academicRecordsHeader: Locator;

  constructor(page: Page) {
    this.page = page;
    this.navbar = new NavbarComponent(page); // Instantiate nested class
    this.academicRecordsHeader = page.locator('.section-title');
  }
}
```

---

### Assignment 3: Refactor Direct Scripts to POM

#### Solution Code (`vjti-portal-refactored.spec.ts`)
```typescript
import { test, expect } from '@playwright/test';
import { VJTI_LoginPage } from './VJTI_LoginPage';
import { DashboardPage } from './DashboardPage';

test('Verify student portal access with POM refactoring', async ({ page }) => {
  // 1. Instantiate page objects
  const loginPage = new VJTI_LoginPage(page);
  const dashboardPage = new DashboardPage(page);

  // 2. Perform page operations via page object methods
  await loginPage.navigate();
  await loginPage.login('vjti-2026-105', '9876');

  // 3. Keep assertions in test script
  await expect(dashboardPage.academicRecordsHeader).toHaveText('Academic Records');

  // 4. Interact with components nested inside page objects
  await dashboardPage.navbar.logout();
  await expect(page).toHaveURL(/.*login/);
});
```

#### Explanation
* **Modular Objects:** The page details are imported from `VJTI_LoginPage` and `DashboardPage`.
* **Zero Hardcoded Selectors:** The test script contains no CSS selectors (`#username`, `#logout-btn`). If HTML IDs update, the test script remains unchanged.
* **Encapsulated Navbar:** Logging out is called via the nested navbar component: `dashboardPage.navbar.logout()`.
