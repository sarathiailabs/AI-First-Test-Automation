# Day 7: Playwright Fundamentals + Locators – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of Playwright configuration, browser contexts, test runner options, and element locator strategies (CSS, XPath, and accessibility locators).

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the difference between a **Browser**, a **Browser Context**, and a **Page** in Playwright? Why is this structure preferred over starting a fresh browser instance for every test?

### Question 2 🔥 **Frequently Asked**
Why does Playwright recommend using accessibility-based locators like `getByRole()` and `getByLabel()` over custom CSS selectors or XPaths? Under what circumstances would you still use CSS or XPath?

### Question 3 📌 **Important**
What is **Shadow DOM**? Explain how Playwright handles Shadow DOM encapsulation differently compared to Selenium WebDriver.

### Question 4 📌 **Important**
Explain **Locator Chaining**. Write a code snippet showing how you would locate a button with the text "Details" inside a card element that has a class of `.product-card-active`.

### Question 5 💡 **Good to Know**
How does the `playwright.config.ts` file manage workers? What are the implications of setting `workers: 1` versus `workers: 4` on test execution speed and environment isolation?

---

## Practical Assignments

### Assignment 1: Launch Browser & Validate Page Title

* **Interview Relevance:** Verifies your ability to write a basic executable Playwright test from scratch, structure a spec file, and perform a basic title validation assertion.
* **Difficulty Level:** Beginner
* **Concepts Covered:** Test runner setup, importing `@playwright/test`, page navigation, title assertions, headless configurations.

#### Problem Statement
Write a clean, executable Playwright test file named `vjti-title.spec.ts` that navigates to the VJTI Student ERP portal, retrieves the page title, and validates that it matches the expected title.

#### Requirements
1. Create a spec file under a test directory.
2. Structure the test block using Playwright's `test` runner interface.
3. Navigate to `https://erp.vjti.ac.in/portal/login` (or use a mock/local URL).
4. Assert that the page title is exactly `"VJTI Student Portal"`.
5. Run the test in headed mode via the terminal and verify the output.

#### Expected Output
```text
Running 1 test using 1 worker
  1 passed (1.8s)
```

#### Hints
* Use `await expect(page).toHaveTitle('VJTI Student Portal')` for a stable assertion.
* Make sure you launch using the standard `test('test name', async ({ page }) => { ... })` context fixture.

---

### Assignment 2: UI Form Locators Challenge (15 Locator Examples)

* **Interview Relevance:** Finding element locators is 80% of UI automation work. Showing familiarity with user-facing and accessibility locators is a top skill interviewers look for.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `getByRole`, `getByLabel`, `getByPlaceholder`, `getByTestId`, CSS, XPath.

#### Problem Statement
Below is the HTML source code for the VJTI Hostel Allocation Form page. Write 15 distinct, stable, and readable locator strategies in Playwright to select various target elements shown in the form.

**HTML Source Code:**
```html
<div class="hostel-application-form" data-testid="hostel-form-wrapper">
  <h1>VJTI Hostel Allotment Form</h1>
  
  <form id="admission-form">
    <!-- Student Roll -->
    <div class="form-group">
      <label for="student-roll">Roll Number</label>
      <input type="text" id="student-roll" name="rollNo" class="form-control text-input" placeholder="Enter Roll Number" />
    </div>

    <!-- Branch Selection -->
    <div class="form-group">
      <label for="branch-select">Engineering Branch</label>
      <select id="branch-select" class="form-select">
        <option value="cs">Computer Science</option>
        <option value="it">Information Technology</option>
        <option value="mech">Mechanical Engineering</option>
      </select>
    </div>

    <!-- Gender Radios -->
    <div class="form-group gender-selection">
      <span class="section-title">Gender</span>
      <label>
        <input type="radio" name="gender" value="male" /> Male
      </label>
      <label>
        <input type="radio" name="gender" value="female" /> Female
      </label>
    </div>

    <!-- Hostel Choice (Shadow DOM encapsulated component) -->
    <hostel-choice-card>
      #shadow-root
      <div class="shadow-container">
        <input type="checkbox" id="block-a" class="shadow-checkbox" />
        <label for="block-a">Prefer Block A Hostel Room</label>
      </div>
    </hostel-choice-card>

    <!-- Declaration Checkbox -->
    <div class="form-check">
      <input type="checkbox" id="declaration-check" class="check-box" />
      <label for="declaration-check">I agree to the VJTI Hostel Rules & Regulations</label>
    </div>

    <!-- Action Buttons -->
    <div class="button-row">
      <button type="reset" class="btn btn-secondary">Reset Form</button>
      <button type="submit" class="btn btn-primary" data-testid="submit-form-btn">Submit Application</button>
    </div>
  </form>
</div>
```

#### Requirements
Write the exact Playwright locator script strings for the following targets:
1. Target the Title Header using `getByRole`.
2. Target the Roll Number Input using `getByLabel`.
3. Target the Roll Number Input using `getByPlaceholder`.
4. Target the Roll Number Input using CSS ID selector.
5. Target the Branch Dropdown using `getByLabel`.
6. Target the Branch Dropdown using CSS tag-and-class selector.
7. Target the "Male" Radio Button using `getByRole`.
8. Target the "Female" Radio Button using `getByRole`.
9. Target the "Prefer Block A" checkbox inside the Shadow DOM.
10. Target the Declaration Checkbox using `getByLabel`.
11. Target the Declaration Checkbox using standard XPath.
12. Target the Reset Button using `getByRole`.
13. Target the Submit Button using `getByRole`.
14. Target the Submit Button using `getByTestId`.
15. Target the complete Form Wrapper Container using `getByTestId`.

#### Expected Output
A list of 15 JS locator expressions matching the HTML code.
*Example:* `const title = page.getByRole('heading', { name: '...' })`

#### Hints
* For roles, inputs like text boxes are `"textbox"`, dropdowns are `"combobox"`, check boxes are `"checkbox"`, and radio buttons are `"radio"`.
* Playwright pierces Shadow DOMs automatically; you can query `#block-a` or `"checkbox"` directly.

---

### Assignment 3: Dynamic Kirana Cart & Chained Locators

* **Interview Relevance:** In real applications, list elements are dynamic. Finding a button inside a specific item card is a frequent task in UI automation.
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** Locator chaining, filtering, dynamic lists.

#### Problem Statement
You are automating test scenarios for the local Kirana Grocery portal's checkout page. The cart item list changes dynamically based on the student's shopping list. Find and target the "Delete" button only for `"Amul Butter"` inside the dynamic list.

**Dynamic HTML Cart Markup:**
```html
<div class="shopping-cart-container">
  <div class="cart-item-row" data-id="item-1">
    <span class="item-name">Aashirvaad Atta 5kg</span>
    <span class="price">₹260</span>
    <button class="btn-remove">Delete</button>
  </div>
  <div class="cart-item-row" data-id="item-2">
    <span class="item-name">Amul Butter 500g</span>
    <span class="price">₹275</span>
    <button class="btn-remove">Delete</button>
  </div>
  <div class="cart-item-row" data-id="item-3">
    <span class="item-name">Tata Salt 1kg</span>
    <span class="price">₹28</span>
    <button class="btn-remove">Delete</button>
  </div>
</div>
```

#### Requirements
1. Construct a chained locator that finds the cart item row containing the text `"Amul Butter 500g"`.
2. Locate the "Delete" button *within* that specific row scope.
3. Write an assertion checking that clicking this specific button targets the item.

#### Expected Output
A single robust chained locator snippet in Playwright:
```javascript
const deleteAmulButterBtn = page...
```

#### Hints
* Use `page.locator('.cart-item-row')` combined with the `.filter({ hasText: '...' })` API.
* Chain the locator using `.getByRole('button', { name: 'Delete' })` at the end of the filter.
