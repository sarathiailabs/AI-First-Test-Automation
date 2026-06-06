# Day 7: Playwright Fundamentals + Locators – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and practical assignments from Day 7.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is the difference between a **Browser**, a **Browser Context**, and a **Page** in Playwright? Why is this structure preferred over starting a fresh browser instance for every test?

### Answer
* **Browser:** A Browser object represents a launched browser binary instance (e.g. launching Chromium, Firefox, or WebKit binaries). This process is expensive, taking 1–3 seconds to load.
* **Browser Context:** A Browser Context is an isolated incognito session inside a launched Browser instance. It holds separate cookies, local storage, cache, and session states. Creating a context takes only milliseconds and consumes negligible system memory.
* **Page:** A Page represents a single tab (or window) running inside a Browser Context. It is where you perform page actions like clicks, navigations, and text entries.
* **Hinglish Helper:** *Browser ek overall software instance hai, Browser Context uske andar chalne wala isolated incognito mode hai, aur Page us incognito session ka ek active tab hai.*

#### Why it is Preferred:
Instead of launching and closing the browser binary for every test file (which slows down execution), Playwright launches the browser **once** and spins up a separate **Browser Context** for each test. This provides 100% test isolation (no cookies leak from test A to test B) at lightning speeds, allowing hundreds of tests to execute concurrently in parallel contexts.

---

### Question 2 🔥 **Frequently Asked**
Why does Playwright recommend using accessibility-based locators like `getByRole()` and `getByLabel()` over custom CSS selectors or XPaths? Under what circumstances would you still use CSS or XPath?

### Answer
#### Why Accessibility-Based Locators are Recommended:
1. **User Realism:** They mirror how real users interact with the page. A user looks for a button labeled "Pay Fees" or a text field with the label "Roll Number", not a `div.form-control > input[type='text']`.
2. **Robustness:** HTML structure and style class names change frequently during design updates. Accessibility properties (like labels and semantic roles) are much more stable.
3. **Accessibility Testing:** If a test cannot find an element using `getByRole('button', { name: 'Submit' })`, it means screen-readers for visually impaired users will also fail to read it. It enforces accessibility compliance.

#### When to Still Use CSS or XPath:
* You should use CSS/XPath when targeting elements that do not have semantic roles or labels (e.g., locating a decorative icon, tracking layout elements like wrappers, or navigating up the DOM tree to parents which accessibility locators cannot do).

---

### Question 3 📌 **Important**
What is **Shadow DOM**? Explain how Playwright handles Shadow DOM encapsulation differently compared to Selenium WebDriver.

### Answer
* **Shadow DOM** is an HTML specification that allows developers to encapsulate styling and HTML layout inside an isolated tree structure (used widely in reusable Web Components). Elements inside a shadow root are protected from global page styles and scripts.
* **Selenium WebDriver Handling:** Selenium cannot see inside a shadow root by default. A QA must write custom JavaScript execution scripts to fetch the shadow root host node first, extract the shadow root object, and query selectors inside it manually.
* **Playwright Handling:** Playwright's locator search engines **automatically pierce shadow root boundaries** by default. To target an element `#block-a` hidden inside a custom web component shadow root, you simply write `page.locator('#block-a')`—no extra steps or JS overrides are needed.

---

### Question 4 📌 **Important**
Explain **Locator Chaining**. Write a code snippet showing how you would locate a button with the text "Details" inside a card element that has a class of `.product-card-active`.

### Answer
* **Locator Chaining** is the practice of joining multiple locator queries together in sequence. This restricts the DOM search scope to the boundaries of the parent locator, helping you find elements inside a specific section without matching duplicates on the rest of the page.
* **Code Snippet:**
```javascript
// Scope matches only the button inside the active card
const detailsButton = page.locator('.product-card-active').getByRole('button', { name: 'Details' });
```

---

### Question 5 💡 **Good to Know**
How does the `playwright.config.ts` file manage workers? What are the implications of setting `workers: 1` versus `workers: 4` on test execution speed and environment isolation?

### Answer
* **Workers** are separate operating system processes launched by Playwright to run tests. Each worker runs a single test at a time in its own isolated browser context.
* **workers: 1**: Tests run sequentially, one after another. Execution speed is slower, but it prevents database bottlenecks if tests share state (like reading/writing to the same student record).
* **workers: 4**: Playwright launches 4 parallel processes. Tests run concurrently, making execution up to 4 times faster. Each worker has complete browser context isolation, so browser cookies will not overlap. However, if the tests write data to a shared backend database simultaneously, it can cause transaction collisions.

---

## Programming Assignment Solutions

### Assignment 1: Launch Browser & Validate Page Title

Below is the complete, executable Playwright test script file `vjti-title.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Validate VJTI Portal Page Title', async ({ page }) => {
  // 1. Navigate to the login portal
  await page.goto('https://erp.vjti.ac.in/portal/login');

  // 2. Validate that the page title is exactly as expected
  await expect(page).toHaveTitle('VJTI Student Portal');
});
```

#### Explanation
1. We import `test` and `expect` from the Playwright test library.
2. We write a standard test block. The `{ page }` parameter is a built-in test fixture that automatically launches the browser, creates a browser context, opens a new tab, and closes it when the test ends.
3. `page.goto()` sends an HTTP GET request to load the website.
4. `expect(page).toHaveTitle()` checks the browser title. It includes built-in retries (waiting for the title to update if the page is rendering dynamically).

---

### Assignment 2: UI Form Locators Challenge (15 Locator Examples)

Here are the 15 distinct locator scripts matching the HTML source code:

#### Solution Code
```javascript
// 1. Target the Title Header using getByRole
const titleHeader = page.getByRole('heading', { name: 'VJTI Hostel Allotment Form' });

// 2. Target the Roll Number Input using getByLabel
const rollInputByLabel = page.getByLabel('Roll Number');

// 3. Target the Roll Number Input using getByPlaceholder
const rollInputByPlaceholder = page.getByPlaceholder('Enter Roll Number');

// 4. Target the Roll Number Input using CSS ID selector
const rollInputByCSS = page.locator('#student-roll');

// 5. Target the Branch Dropdown using getByLabel
const branchSelectByLabel = page.getByLabel('Engineering Branch');

// 6. Target the Branch Dropdown using CSS tag-and-class selector
const branchSelectByCSS = page.locator('select.form-select');

// 7. Target the "Male" Radio Button using getByRole
const maleRadio = page.getByRole('radio', { name: 'Male' });

// 8. Target the "Female" Radio Button using getByRole
const femaleRadio = page.getByRole('radio', { name: 'Female' });

// 9. Target the "Prefer Block A" checkbox inside the Shadow DOM (Automatic piercing)
const shadowCheckbox = page.locator('#block-a');

// 10. Target the Declaration Checkbox using getByLabel
const declarationCheckByLabel = page.getByLabel('I agree to the VJTI Hostel Rules & Regulations');

// 11. Target the Declaration Checkbox using standard XPath
const declarationCheckByXPath = page.locator("//input[@id='declaration-check']");

// 12. Target the Reset Button using getByRole
const resetButton = page.getByRole('button', { name: 'Reset Form' });

// 13. Target the Submit Button using getByRole
const submitButtonByRole = page.getByRole('button', { name: 'Submit Application' });

// 14. Target the Submit Button using getByTestId
const submitButtonByTestId = page.getByTestId('submit-form-btn');

// 15. Target the complete Form Wrapper Container using getByTestId
const formWrapper = page.getByTestId('hostel-form-wrapper');
```

#### Explanation
* **Role Mappings:** The inputs automatically map to standard accessibility roles (`'textbox'` for text inputs, `'combobox'` for select dropdowns, `'radio'` for radios, `'checkbox'` for checkboxes, `'button'` for reset/submit triggers).
* **Shadow DOM:** The selector `#block-a` works directly. Playwright automatically pierces `<hostel-choice-card>` shadow root boundaries to locate it without calling custom shadow APIs.
* **Test IDs:** Target `getByTestId` maps directly to elements with the `data-testid` attribute, creating a robust, decoupled locator.

---

### Assignment 3: Dynamic Kirana Cart & Chained Locators

Below is the implementation of the chained filter locator to isolate the delete action on the target dynamic product.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Delete specific item from dynamic shopping cart', async ({ page }) => {
  await page.goto('https://kiranastore-checkout.local'); // Mock checkout URL

  // 1. Locate the container row containing "Amul Butter 500g" using filter
  const amulButterRow = page.locator('.cart-item-row').filter({ hasText: 'Amul Butter 500g' });

  // 2. Chain the locator to find the "Delete" button inside that specific row
  const deleteButton = amulButterRow.getByRole('button', { name: 'Delete' });

  // 3. Perform the click action
  await deleteButton.click();

  // 4. Assert that the item is removed (it should detach from the DOM)
  await expect(amulButterRow).toBeHidden();
});
```

#### Explanation
1. **`.locator('.cart-item-row')`**: Matches all three cart item rows in the container.
2. **`.filter({ hasText: 'Amul Butter 500g' })`**: Out of the three matched rows, this filters down to the single row that contains the visible text `"Amul Butter 500g"`.
3. **`.getByRole('button', { name: 'Delete' })`**: Chains a search *inside* that specific row to find the button labeled "Delete". This guarantees we don't accidentally click the delete button for "Aashirvaad Atta" or "Tata Salt".
4. **`expect().toBeHidden()`**: Verifies that the row has disappeared from the active view, confirming successful deletion.
