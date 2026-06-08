# Day 13: Framework Design – Assignments

This assignment file contains theoretical questions and practical programming tasks designed to reinforce framework architecture, Page Object Model (POM) patterns, Component models, Base Page inheritance, utilities, and error logs.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the Page Object Model (POM)? Explain how it improves the maintainability and readability of an automation test suite.

### Question 2 🔥 **Frequently Asked**
Why is it considered a bad design pattern to put assertions (`expect()`) directly inside Page Object classes? Where should assertions reside?

### Question 3 📌 **Important**
What is the difference between POM and the Component Object Model? How does nesting components inside page classes help reduce code duplication?

### Question 4 📌 **Important**
What type of locator declarations and action methods belong in a `BasePage` class? When should a page class *not* inherit from `BasePage`?

### Question 5 💡 **Good to Know**
Explain how empty `catch` blocks in Try-Catch error handling can hide actual application bugs. What are the best practices for handling errors in automated scripts?

---

## Practical Assignments

### Assignment 1: VJTI Login Page Object Model

* **Interview Relevance:** Implementing a POM class from scratch is a standard test for junior QA automation roles. Verifies understanding of locator initialization and encapsulated action methods.
* **Difficulty Level:** Easy-Intermediate
* **Concepts Covered:** Class definitions, constructors, locators initialization, custom page actions.

#### Problem Statement
Create a Page Object class named `VJTI_LoginPage.ts` representing the login page of VJTI College portal. The class must define locators and a login action method.

**Login Form HTML Markup:**
```html
<form id="vjti-login-form">
  <input type="text" id="username" placeholder="Enter Registration No" />
  <input type="password" id="student-pin" placeholder="Enter PIN" />
  <button type="submit" class="btn-primary" id="login-submit">Login Portal</button>
</form>
```

#### Requirements
1. Define a class `VJTI_LoginPage`.
2. Initialize locators in the constructor for the username input, PIN input, and submit button.
3. Write an action method `navigate()` that redirects the browser to `https://api.vjti.edu/login`.
4. Write an action method `login(regNo, pin)` that fills the input fields and clicks the submit button.
5. Export the page class.

#### Hints
* Use type imports for Playwright's `Page` and `Locator`.

---

### Assignment 2: VJTI Dashboard Page & Reusable Navbar Component

* **Interview Relevance:** Creating modular component classes nested inside page classes is a key skill for building scalable frameworks.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Component classes, nested instances, selector scopes.

#### Problem Statement
Create a Navbar Component class and nest it inside a Dashboard Page class to represent the VJTI dashboard.

**Portal Layout Markup:**
```html
<div class="dashboard-container">
  <nav class="portal-navbar">
    <span class="user-greeting">Welcome, Suresh</span>
    <button id="logout-btn">Log Out</button>
  </nav>

  <main class="dashboard-content">
    <h2 class="section-title">Academic Records</h2>
    <div class="marks-summary-grid">...</div>
  </main>
</div>
```

#### Requirements
1. Create a component class `NavbarComponent` with locators for the user greeting text and logout button. Add a method `logout()`.
2. Create a page class `DashboardPage`.
3. Nest the `NavbarComponent` instance inside the `DashboardPage` constructor.
4. Add a locator inside `DashboardPage` for the academic records header (`.section-title`).
5. Export both classes.

#### Hints
* Initialize components by passing the page context: `this.navbar = new NavbarComponent(page)`.

---

### Assignment 3: Refactor Direct Scripts to POM

* **Interview Relevance:** Real-world QA projects often involve refactoring legacy "spaghetti" tests into clean POM architectures.
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** Test refactoring, POM instantiation, clean script execution.

#### Problem Statement
Refactor the legacy script below into a clean Page Object Model architecture using the `VJTI_LoginPage` and `DashboardPage` classes created in the previous assignments.

**Legacy Script (`vjti-portal.spec.ts`):**
```typescript
import { test, expect } from '@playwright/test';

test('Verify student portal access', async ({ page }) => {
  // Legacy direct scripting logic
  await page.goto('https://api.vjti.edu/login');
  
  await page.locator('#username').fill('vjti-2026-105');
  await page.locator('#student-pin').fill('9876');
  await page.locator('#login-submit').click();

  await expect(page.locator('.section-title')).toHaveText('Academic Records');
  
  await page.locator('#logout-btn').click();
  await expect(page).toHaveURL(/.*login/);
});
```

#### Requirements
1. Create a refactored script named `vjti-portal-refactored.spec.ts`.
2. Import the `VJTI_LoginPage` and `DashboardPage` page objects.
3. Replace all direct locator calls with actions called from page object instances.
4. Keep the assertions (`expect()`) inside the test script.

#### Hints
* Instantiate the page objects inside the test block:
  ```typescript
  const loginPage = new VJTI_LoginPage(page);
  const dashboardPage = new DashboardPage(page);
  ```
