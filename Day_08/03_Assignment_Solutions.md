# Day 8: UI Actions + Assertions – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and practical assignments from Day 8.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is the difference between a **Hard Assertion** and a **Soft Assertion** in Playwright? Provide a real-world scenario where you would select one over the other.

### Answer
* **Hard Assertion:** Standard `expect()` checks. If a hard assertion fails, execution stops immediately, skipping all subsequent lines in that test block.
  * **Scenario:** Verifying login success. If `await expect(page).toHaveURL(/dashboard/)` fails, it makes no sense to run the remaining clicks inside the dashboard since the user isn't logged in. A hard assertion is mandatory here.
* **Soft Assertion:** Written as `expect.soft()`. If a soft assertion fails, Playwright logs the failure but continues running the subsequent steps of the test. All accumulated failures are reported at the end of the test execution.
  * **Scenario:** Verifying a list of course cards on a dashboard page. If one card has a styling layout issue or a spelling typo, we want to know about it, but we still want the script to check all other cards and links on the page. A soft assertion is ideal here.
* **Hinglish Helper:** *Hard assertion fail hone par test wahi ruk jata hai, Soft assertion fail hone par errors note ho jate hain par test aage chalta rehta hai.*

---

### Question 2 🔥 **Frequently Asked**
What are **Actionability Checks**? List at least four checks that Playwright performs on an element automatically before executing a click action.

### Answer
**Actionability Checks** are Playwright's built-in checks that run automatically before performing an action (like a click or fill) to ensure the target element is ready to receive the interaction. This completely eliminates the need for hard sleep timers.

Before performing `click()`, Playwright checks that the element is:
1. **Attached:** Present in the active page DOM.
2. **Visible:** Visible on the screen (not hidden, display none, or zero opacity).
3. **Stable:** Not moving or animating (waiting for transitions to stop).
4. **Enabled:** Not disabled (doesn't have `disabled` or `aria-disabled` attributes).
5. **Receiving Pointer Events:** Not obscured by other elements (like load spinners, dialog panels, or header banners).

---

### Question 3 📌 **Important**
Why is the usage of `page.waitForTimeout()` highly discouraged in production-grade test automation suites? What are the better alternatives for handling dynamic loading states?

### Answer
#### Why `waitForTimeout()` is Discouraged (Flaky Sleep):
* It pauses execution for a **fixed, hardcoded duration** (e.g. `await page.waitForTimeout(5000)` pauses for exactly 5 seconds).
* **Inconvenience:** If the network is fast and the element loads in 100 milliseconds, your test is wasting 4.9 seconds waiting for nothing. If the network is slow and the element takes 5.1 seconds, your test will crash anyway. This makes tests both slow and flaky.

#### Better Alternatives (Dynamic Wait):
1. **Web-First Assertions:** Expectation statements auto-retry for up to 5 seconds by default:
   ```javascript
   await expect(page.locator('#result-card')).toBeVisible();
   ```
2. **Explicit Wait for States:** Wait for specific elements to appear or disappear:
   ```javascript
   await page.locator('#loading-spinner').waitFor({ state: 'hidden' });
   ```

---

### Question 4 📌 **Important**
Explain the differences between the **Load**, **DOMContentLoaded**, and **Network Idle** wait states in navigation. When is using `networkidle` risky?

### Answer
* **DOMContentLoaded:** Triggered when the browser finishes parsing the basic HTML layout and building the DOM tree. Script files can run, but images and CSS styles might still be loading. (Fastest).
* **Load:** Triggered when the complete page structure, stylesheets, scripts, and external assets (like images and fonts) have finished downloading. This is Playwright's default wait state.
* **Network Idle:** Triggered when there have been **no new network connections** active for at least 500 milliseconds.

#### Why `networkidle` is Risky:
* If the page uses analytics tracking scripts, recurring heartbeats, or polling logs that check back with the server every 2 seconds, the network traffic will never be idle for 500ms. In this case, `page.waitForLoadState('networkidle')` will hang indefinitely until it triggers a test timeout failure.

---

### Question 5 💡 **Good to Know**
How does Playwright handle scroll actions under the hood during UI interactions? Do you need to scroll to an element manually before clicking it?

### Answer
* **Automatic Scrolling:** Playwright has built-in auto-scrolling. Before performing any action (like a click, hover, or check) on an element, Playwright automatically scrolls the page to bring the target element into the browser's visible viewport.
* **Manual Scroll Requirement:** You do **not** need to manually scroll to an element before interacting with it.
* *Note:* You only need to explicitly write scroll commands (like `locator.scrollIntoViewIfNeeded()`) if you are testing layout designs, verifying footers, or triggering infinite scrolls where loading depends on scrolling without clicking.

---

## Programming Assignment Solutions

### Assignment 1: VJTI ERP Login Automation

Below is the complete, executable Playwright test script file `vjti-login.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Automate VJTI Student ERP Login Flow', async ({ page }) => {
  // 1. Navigate to the login portal page
  await page.goto('https://erp.vjti.ac.in/portal/login');

  // 2. Fill in input credentials
  await page.locator('#roll-number').fill('1005');
  await page.locator('#student-pass').fill('vjtiSecure123!');

  // 3. Click the submit button
  await page.locator('#submit-login-btn').click();

  // 4. Assert that URL redirection is correct
  await expect(page).toHaveURL(/.*dashboard\/student/);

  // 5. Assert that the dashboard welcome message is visible
  const welcomeMessage = page.getByText('Welcome, Student');
  await expect(welcomeMessage).toBeVisible();
});
```

#### Explanation
* **`fill()`**: Auto-waits for `#roll-number` to attach, clears existing text, and enters `'1005'`.
* **`click()`**: Performs actionability checks on `#submit-login-btn` and clicks it.
* **`toHaveURL()`**: Asserts that the final redirect URL matches the `/.*dashboard\/student/` regular expression, retrying dynamically if the redirection takes a second.

---

### Assignment 2: Course Registry Search (Soft & Hard Assertions)

Below is the complete, executable Playwright test script file `course-search.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Search Course Registry and verify items', async ({ page }) => {
  // 1. Navigate to the course portal registry page
  await page.goto('https://erp.vjti.ac.in/portal/courses');

  // 2. Locate the search input, fill search text, and press Enter keyboard key
  const searchInput = page.getByPlaceholder('Search Courses');
  await searchInput.fill('Data Structures');
  await page.keyboard.press('Enter');

  // 3. Define the locator representing returned course cards
  const courseCards = page.locator('.course-card');

  // 4. HARD ASSERTION: Verify count is exactly 3. Test stops instantly if this fails.
  await expect(courseCards).toHaveCount(3);

  // 5. SOFT ASSERTIONS: Check individual card details; test continues even if one fails.
  await expect.soft(courseCards.nth(0)).toContainText('Algorithms');
  await expect.soft(courseCards.nth(1).locator('.difficulty')).toHaveText('Medium');
  await expect.soft(courseCards.nth(2)).toContainText('in C');
});
```

#### Explanation
* **`page.keyboard.press('Enter')`**: Simulates the physical keyboard keypress to submit the query.
* **`toHaveCount(3)`**: Acts as a hard assertion. If the search fails or returns only 1 card, the test stops immediately to avoid wasting time checking indexes that don't exist.
* **`expect.soft()`**: Gathers layout checks. If card 1 says "Advanced" instead of "Hard", the failure is noted, but card 2 and 3 are still checked.

---

### Assignment 3: Kirana Shop Hover & Scroll Interactions

Below is the complete, executable Playwright test script file `kirana-checkout.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Scroll down, hover to reveal, and add item to cart', async ({ page }) => {
  // 1. Navigate to the shopping page
  await page.goto('https://kiranastore-checkout.local');

  // 2. Select product card element
  const amulButterCard = page.locator('[data-id="butter-card"]');

  // 3. Scroll down until the target card is inside the viewport
  await amulButterCard.scrollIntoViewIfNeeded();

  // 4. Hover cursor over the product card to reveal the hidden Quick Add button
  await amulButterCard.hover();

  // 5. Locate the newly visible Quick Add button inside the card scope and click it
  const quickAddBtn = amulButterCard.locator('.add-to-cart-trigger');
  await quickAddBtn.click();

  // 6. Assert that the cart badge count text has updated to "1 Items"
  const cartBadge = page.getByTestId('cart-badge');
  await expect(cartBadge).toHaveText('1 Items');
});
```

#### Explanation
* **`scrollIntoViewIfNeeded()`**: Positions the browser screen to view the product in the footer.
* **`hover()`**: Moves the pointer over the card, which changes styling classes (display: block) to reveal the child trigger `.add-to-cart-trigger`.
* **`toHaveText('1 Items')`**: Asserts that the shopping cart icon updates to display the new item count.
