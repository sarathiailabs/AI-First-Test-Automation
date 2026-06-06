# Day 8: UI Actions + Assertions – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of basic UI interactions (click, fill, hover, scroll), dynamic synchronization (auto-wait, explicit wait, load states), and validation assertions (hard vs. soft).

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the difference between a **Hard Assertion** and a **Soft Assertion** in Playwright? Provide a real-world scenario where you would select one over the other.

### Question 2 🔥 **Frequently Asked**
What are **Actionability Checks**? List at least four checks that Playwright performs on an element automatically before executing a click action.

### Question 3 📌 **Important**
Why is the usage of `page.waitForTimeout()` highly discouraged in production-grade test automation suites? What are the better alternatives for handling dynamic loading states?

### Question 4 📌 **Important**
Explain the differences between the **Load**, **DOMContentLoaded**, and **Network Idle** wait states in navigation. When is using `networkidle` risky?

### Question 5 💡 **Good to Know**
How does Playwright handle scroll actions under the hood during UI interactions? Do you need to scroll to an element manually before clicking it?

---

## Practical Assignments

### Assignment 1: VJTI ERP Login Automation

* **Interview Relevance:** A classic smoke test automation flow. Demonstrates capability to structure navigation, form fill, click, and session redirection checkpoints.
* **Difficulty Level:** Beginner
* **Concepts Covered:** `goto`, `fill`, `click`, hard assertions, URL verification.

#### Problem Statement
Write a Playwright test file named `vjti-login.spec.ts` that automates logging into the VJTI ERP Portal. After submitting credentials, assert that the browser successfully navigates to the student dashboard.

**HTML Markup of Login Form:**
```html
<form id="login-portal-form">
  <input type="text" id="roll-number" class="input-field" placeholder="Roll Number" />
  <input type="password" id="student-pass" class="input-field" placeholder="Password" />
  <button type="submit" id="submit-login-btn">Sign In</button>
</form>
```

#### Requirements
1. Structure the test block using Playwright's `test` interface.
2. Navigate to `https://erp.vjti.ac.in/portal/login` (or use a mock URL).
3. Fill the Roll Number input with `"1005"`.
4. Fill the Password input with `"vjtiSecure123!"`.
5. Click the "Sign In" button.
6. Assert that the page URL dynamically redirects to include `/dashboard/student`.
7. Assert that a dashboard welcome message element containing `"Welcome, Student"` is visible.

#### Expected Output
A passed execution log:
```text
Running 1 test using 1 worker
  1 passed (2.1s)
```

#### Hints
* Use `await expect(page).toHaveURL(/.*dashboard\/student/)` to assert URL redirection.
* Ensure you click the correct button using a stable locator.

---

### Assignment 2: Course Registry Search (Soft & Hard Assertions)

* **Interview Relevance:** Tests your ability to handle search result synchronization and apply both hard assertions (critical counts) and soft assertions (cosmetic/item validation) in a single test.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Keyboard actions, waiting for search completion, hard assertions, soft assertions.

#### Problem Statement
Write a Playwright test named `course-search.spec.ts` that automates searching the VJTI Course Registry. You must search for "Data Structures" and validate that exactly 3 matching courses are returned (hard assertion), while verifying each course's card details individually (soft assertions).

**Expected Results Mock HTML:**
```html
<div class="search-results-section">
  <h2>Search Results</h2>
  <div class="course-list">
    <div class="course-card" data-id="1">
      <h3 class="course-title">Data Structures and Algorithms</h3>
      <span class="difficulty">Hard</span>
    </div>
    <div class="course-card" data-id="2">
      <h3 class="course-title">Basic Data Structures</h3>
      <span class="difficulty">Medium</span>
    </div>
    <div class="course-card" data-id="3">
      <h3 class="course-title">Data Structures in C</h3>
      <span class="difficulty">Medium</span>
    </div>
  </div>
</div>
```

#### Requirements
1. Navigate to the course search page.
2. Target the search field, input `"Data Structures"`, and press the `Enter` keyboard key.
3. Wait for the `.course-card` elements to be visible.
4. Apply a **Hard Assertion** verifying that the count of `.course-card` elements is exactly `3`.
5. Iterate through the three cards and apply **Soft Assertions** checking:
   * Card 1 contains `"Algorithms"`.
   * Card 2 difficulty displays `"Medium"`.
   * Card 3 title contains `"in C"`.
6. Verify the test passes, logging any soft assertion failures if they occur.

#### Hints
* Use `await expect(page.locator('.course-card')).toHaveCount(3)` as your hard assertion.
* Use `await expect.soft(locator).toContainText(...)` for soft assertions.

---

### Assignment 3: Kirana Shop Hover & Scroll Interactions

* **Interview Relevance:** Tests your capability to handle advanced physical simulations like scrolling and hover states which are common on modern retail and e-commerce checkouts.
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** `scrollIntoViewIfNeeded`, `hover`, hover-revealed triggers, cart badge assertion.

#### Problem Statement
Write a Playwright test named `kirana-checkout.spec.ts` that simulates a student shopping on a local grocery portal. The test must scroll down to find the "Quick Add" cards in the footer, hover over an item card (e.g. `"Amul Butter"`) to reveal a hidden "Add to Cart" button, click it, and assert that the shopping cart icon updates its badge count.

**HTML Layout:**
```html
<!-- Viewport Top -->
<header class="top-nav">
  <div class="cart-icon" data-testid="cart-badge">0 Items</div>
</header>

<main class="page-content" style="height: 1200px;">
  <!-- Space filling content requiring scrolling -->
</main>

<!-- Viewport Bottom (Requires Scrolling) -->
<footer class="quick-add-footer">
  <div class="product-item-card" data-id="butter-card">
    <span class="product-title">Amul Butter 500g</span>
    <div class="hover-actions-panel" style="display: none;">
      <!-- Hidden until card parent is hovered -->
      <button class="add-to-cart-trigger">Quick Add</button>
    </div>
  </div>
</footer>
```

#### Requirements
1. Navigate to the local grocery portal.
2. Scroll down until the footer `.quick-add-footer` or target product card `[data-id="butter-card"]` is in view.
3. Hover your cursor over the product card containing `"Amul Butter 500g"`.
4. Once hovered, locate and click the revealed `"Quick Add"` button.
5. Assert that the cart badge icon text updates from `"0 Items"` to `"1 Items"`.

#### Hints
* Use `await page.locator('[data-id="butter-card"]').scrollIntoViewIfNeeded()` to trigger the scroll action.
* Chain hover: `await page.locator('[data-id="butter-card"]').hover()`.
* Select the badge and assert using `await expect(page.getByTestId('cart-badge')).toHaveText('1 Items')`.
