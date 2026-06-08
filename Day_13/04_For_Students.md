# Day 13: Framework Design – Student Revision Notes

This revision sheet provides a quick-revision summary of framework design rules, code snippets, and implementation steps for all topics covered in Day 13.

---

## Topic Revision

### 1. Framework Design Principles
* **Definition:** Architectural rules (DRY, Single Responsibility, Separation of Concerns) to create structured, clean test suites. *(Clean aur maintainable code likhne ke framework rules).*
* **Key Points:**
  * **DRY:** Never copy-paste selector strings.
  * **SRP:** LoginPage handles login, dashboard handles dashboards.
* **Common Mistakes:** Hardcoding selector strings directly inside test files, leading to fragile suites.

---

### 2. Page Object Model (POM)
* **Definition:** A pattern where page layouts are mapped to class files, holding locators and actions. *(Web pages ko class file me map karna locator/actions define karne ke liye).*
* **Example Syntax:**
  ```typescript
  export class LoginPage {
    readonly page: Page;
    readonly loginBtn: Locator;
    constructor(page: Page) {
      this.loginBtn = page.locator('#submit-btn');
    }
  }
  ```
* **Key Points:**
  * Keep selectors inside page constructors.
  * Define page methods for user actions.
* **Common Mistakes:** Putting test assertions (`expect()`) inside POM classes instead of test script files.

---

### 3. Component Object Model
* **Definition:** Modeling reusable page sections (headers, navigation bars, sidebars) as classes nested inside parent page models. *(NavBar ya headers ko reusable classes me wrap karna).*
* **Example Class Nesting:**
  ```typescript
  export class DashboardPage {
    readonly header: HeaderComponent;
    constructor(page: Page) {
      this.header = new HeaderComponent(page);
    }
  }
  ```
* **Key Points:**
  * Avoids declaring logo or logout locators in multiple page files.
  * Nested component structures match visual user interfaces.

---

### 4. Base Classes
* **Definition:** A parent page object (`BasePage`) containing shared methods (like page loading checks, transitions) that child page classes extend. *(Common actions ko Base class me define karke extend karna).*
* **Example Inheriting Page:**
  ```typescript
  export class FeesPage extends BasePage {
    constructor(page: Page) {
      super(page); // Invokes BasePage constructor
    }
  }
  ```
* **Key Points:**
  * Shares standard setups.
  * Reduces duplicate navigation scripts.

---

### 5. Utilities
* **Definition:** Static processing scripts (Excel readers, string generators, date formatting) that process data independently of the browser DOM. *(Data formatters aur file readers helper utils).*
* **Example Static Utility:**
  ```typescript
  export class DateUtils {
    static getFormattedDate() { return new Date().toDateString(); }
  }
  ```
* **Key Points:**
  * Functions do not require Playwright's `Page` object.

---

### 6. Reusable Components
* **Definition:** Custom classes wrapping complex HTML widgets (like calendar datepickers, search suggestions lists) to simplify interactions. *(DatePicker aur Custom Dropdowns jaise controls ko object me wrap karna).*
* **Key Points:**
  * Keeps test files clean of index operations.
  * Methods receive clean inputs.

---

### 7. Error Handling
* **Definition:** Code wrapper constructs (Try-Catch) to manage execution exceptions gracefully, take diagnostic screenshots, and close database resources. *(Errors handle karne aur failure logs/screenshots generate karne ka setup).*
* **Example Block:**
  ```typescript
  try {
    await expect(page.locator('#success-toast')).toBeVisible();
  } catch (error) {
    await page.screenshot({ path: 'failure.png' });
    throw error;
  }
  ```
* **Common Mistakes:** Leaving `catch` blocks empty, which hides test failures and reports incorrect passes.

---

## Assignment Summary

During this session, we practice:
1. **VJTI LoginPage Object Model:** Defining a Page class holding login inputs and actions.
2. **Dashboard with Nested Navbar:** Creating a component model, nesting it inside a Page Object constructor, and executing actions.
3. **Legacy Script Refactoring:** Removing hardcoded selectors from test files and refactoring tests to use POM models.

---

## Quick Revision Sheet

| Pattern | Target | Code Structure | Reuse Benefit |
| --- | --- | --- | --- |
| **Base Class** | Shared operations | `extends BasePage` | Global navigation / loading waits |
| **POM** | Page element actions | `class LoginPage { ... }` | Decouples UI tags from test logic |
| **Component** | Shared UI layout widget | Nest inside POM: `this.header = ...` | Eliminates header/navbar duplication |
| **Utility** | Static data helper | `DateUtils.getFormattedDate()` | External math or file processing |
| **Error Hand.** | Fail diagnostics | `try { ... } catch (e) { ... }` | Captures failure screenshots |

---

## Important Takeaways

1. **Keep Assertions in Spec Files:** Keep validations separate from POM files. Spec files verify outcomes; POM files model operations.
2. **Single Change Points:** Modifying page classes updates all matching test files automatically.
3. **Modular Components:** Nest components inside page classes to model complex dashboards.
