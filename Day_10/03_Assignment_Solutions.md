# Day 10: Advanced UI Automation – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and practical assignments from Day 10.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
Write a code snippet showing how you locate a cell value inside a dynamic table where rows change order on every page refresh. Describe your selector strategy.

### Answer
#### Code Snippet:
```javascript
// 1. Locate the dynamic table
const studentTable = page.locator('table#student-grid');

// 2. Find the row matching the student name
const targetRow = studentTable.locator('tbody tr').filter({ hasText: 'Amit Sharma' });

// 3. Locate the first column (Roll Number) cell inside the row scope
const rollNoCell = targetRow.locator('td').nth(0);

// 4. Assert cell text value
await expect(rollNoCell).toHaveText('1002');
```

#### Selector Strategy:
We avoid using index-based row selectors (like `tr:nth-child(1)`) because row orders shuffle dynamically.
1. We target all rows (`tr`) inside the table body.
2. We use `.filter({ hasText: 'Amit Sharma' })` to dynamically find the row containing our unique search term, regardless of its row index.
3. Once the row scope is isolated, we look up the column cells (`td`) using index positioning (`.nth(0)` for the first column). This ensures we locate the correct cell even if the row shuffles to the bottom.

---

### Question 2 🔥 **Frequently Asked**
How do you handle autocomplete search boxes (auto-suggestions) in Playwright? Why can using `locator.fill()` sometimes fail to trigger suggestions?

### Answer
#### How to handle Autocomplete Search:
1. Focus on the input field and type the search characters.
2. Wait for the suggestion dropdown card to attach to the DOM and be visible.
3. Click the target option from the matching list.

#### Why `fill()` Can Fail:
* `locator.fill()` enters the entire text string instantly in a single batch update.
* **The issue:** Some custom autocomplete search boxes (built using React or Angular listeners) listen for physical keyboard events (like `keyup` or `keypress` with key-by-key delays). Since `fill()` does not trigger these events, the suggestions dropdown doesn't open.
* **The Solution:** Use `locator.pressSequentially('text')`, which types characters one-by-one, simulating realistic typing delays and triggering keyboard listeners correctly.

---

### Question 3 📌 **Important**
How do you automate and validate file downloads in Playwright? Write a code snippet showing how to capture the download event and save the file to a custom project folder.

### Answer
#### Automation Strategy:
File downloads are handled by setting up an event listener using `page.waitForEvent('download')`. The click action and event listener must run concurrently in a `Promise.all()` block to prevent race conditions.

#### Code Snippet:
```javascript
const path = require('path');

// 1. Set up download listener and click action concurrently
const [download] = await Promise.all([
  page.waitForEvent('download'), // Listen for download trigger
  page.locator('#download-syllabus-btn').click() // Trigger download click
]);

// 2. Define custom destination filepath
const destination = path.join(__dirname, 'downloads', 'syllabus.pdf');

// 3. Save download payload to custom path
await download.saveAs(destination);
```

---

### Question 4 📌 **Important**
Explain **Lazy Loading** and **Infinite Scroll**. How do you write automation scripts to verify that lazy-loaded elements render correctly when scrolled?

### Answer
* **Lazy Loading:** Performance optimization that delays downloading images or components until the user scrolls them into view.
* **Infinite Scroll:** Design pattern that appends new items to the bottom of the list dynamically as the user scrolls, avoiding pagination controls.

#### Automation Strategy:
1. **Scroll Viewport:** Use `page.evaluate(() => window.scrollTo(0, document.body.scrollHeight))` to scroll down the page dynamically, or use `locator.scrollIntoViewIfNeeded()` to target a specific placeholder element.
2. **Sync State:** Wait for loading indicators (spinners) to detach, or wait for the element count to increase:
   ```javascript
   const cardList = page.locator('.product-card');
   const initialCount = await cardList.count();
   
   // Scroll down
   await page.evaluate(() => window.scrollTo(0, document.body.scrollHeight));
   
   // Assert count increases as new items load
   await expect(cardList).toHaveCount(initialCount + 10);
   ```
3. **Verify Element Attributes:** Assert that placeholder images swap their source attributes to the real URLs:
   ```javascript
   await expect(page.locator('img.avatar').nth(5)).toHaveAttribute('src', /.*avatar-image.png/);
   ```

---

### Question 5 💡 **Good to Know**
What is the difference between **Test Retries** and **Action Retries**? How does configuring retries help manage flaky tests in CI pipelines, and what are the limitations?

### Answer
* **Action Retries:** Playwright's built-in retry mechanism for individual actions (like click or fill). If Playwright cannot find an element or it fails actionability checks, it retries the check automatically for up to 30 seconds before throwing an error.
* **Test Retries:** Configuration setting that re-runs the entire failed test block (from start to finish) up to a set number of times:
  * *Config: `retries: 2`*
* **Implications in CI:** In CI/CD pipelines, temporary network latency or slow API responses can cause tests to fail. Retrying failed tests automatically handles these temporary glitches, preventing pipelines from showing red failures.
* **Limitations:** Retries hide flakiness but do not fix it. If a test has fragile locators, retrying it just hides the bug. Always fix the locator stability first instead of relying on retries to pass the pipeline.

---

## Programming Assignment Solutions

### Assignment 1: VJTI Student Registry Dynamic Table & Pagination

Below is the complete, executable Playwright test script file `vjti-table.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Search Dynamic Table, Extract Roll No, and Navigate Pagination', async ({ page }) => {
  await page.goto('https://erp.vjti.ac.in/portal/registry');

  // 1. Search student name
  const searchInput = page.locator('#search-student');
  await searchInput.fill('Sharma');

  // 2. Locate the matching student row dynamically using filter
  const matchingRow = page.locator('#student-grid tbody tr').filter({ hasText: 'Amit Sharma' });

  // 3. Extract Roll Number value from the first column cell (index 0)
  const rollNoCell = matchingRow.locator('td').nth(0);
  await expect(rollNoCell).toHaveText('1002');

  // 4. Clear the search field to reset the table
  await searchInput.fill('');

  // 5. Save a reference to a page-1 row to verify page transition later
  const firstPageRow = page.locator('#student-grid tbody tr').nth(0);

  // 6. Click the Next Page pagination button
  await page.locator('#next-btn').click();

  // 7. Wait for page-1 data rows to detach from the DOM (ensuring page transition completes)
  await firstPageRow.waitFor({ state: 'detached', timeout: 5000 });

  // 8. Assert that page-2 student records are visible
  const page2Record = page.locator('#student-grid tbody tr').nth(0);
  await expect(page2Record).toBeVisible();
});
```

#### Explanation
* **`filter({ hasText: ... })`**: Finds the row containing the target text dynamically, avoiding hardcoded row index selections.
* **`waitFor({ state: 'detached' })`**: Confirms that the page-1 elements have detached from the DOM, verifying that the new page data loaded successfully.

---

### Assignment 2: Syllabus PDF Download & Validation

Below is the complete, executable Playwright test script file `vjti-download.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';
import path from 'path';
import fs from 'fs';

test('Download syllabus PDF and verify filesystem status', async ({ page }) => {
  await page.goto('https://erp.vjti.ac.in/portal/syllabus');

  // 1. Setup download event listener and click action concurrently
  const [download] = await Promise.all([
    page.waitForEvent('download'),
    page.locator('#download-cs-syllabus-btn').click()
  ]);

  // 2. Define local destination path inside the workspace
  const localDestFolder = path.join(__dirname, 'downloads');
  if (!fs.existsSync(localDestFolder)) {
    fs.mkdirSync(localDestFolder);
  }
  const filePath = path.join(localDestFolder, 'syllabus.pdf');

  // 3. Save download payload to target folder
  await download.saveAs(filePath);

  // 4. ASSERTION: Check that the file exists in the directory
  const fileExists = fs.existsSync(filePath);
  expect(fileExists).toBe(true);

  // 5. ASSERTION: Verify the file has non-zero size
  const stats = fs.statSync(filePath);
  expect(stats.size).toBeGreaterThan(0);

  // 6. CLEAN UP: Delete the downloaded file after verification
  if (fs.existsSync(filePath)) {
    fs.unlinkSync(filePath);
  }
});
```

#### Explanation
* **`page.waitForEvent('download')`**: Listens for the browser download trigger.
* **`download.saveAs(filePath)`**: Saves the downloaded file in the project folder.
* **`fs.existsSync()`** / **`fs.statSync()`**: Verify the file was saved correctly.

---

### Assignment 3: Multi-Step Scholarship Application Form

Below is the complete, executable Playwright test script file `vjti-scholarship.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Automate Multi-Step Scholarship Application Form Wizard', async ({ page }) => {
  await page.goto('https://erp.vjti.ac.in/portal/scholarship');

  // 1. Assert step-1 state
  const indicator = page.locator('.step-indicator');
  await expect(indicator).toHaveText('Step 1 of 3');

  // 2. Fill step-1 details and click Next
  await page.locator('#parent-income').fill('450000');
  await page.getByRole('button', { name: 'Next' }).click();

  // 3. Assert step-2 transition
  await expect(indicator).toHaveText('Step 2 of 3');

  // 4. Select step-2 GPA dropdown option and click Next
  await page.locator('#marks-gpa').selectOption('9');
  await page.getByRole('button', { name: 'Next' }).click();

  // 5. Assert step-3 transition
  await expect(indicator).toHaveText('Step 3 of 3');

  // 6. Click Submit Application on step-3
  await page.locator('#submit-application-btn').click();

  // 7. ASSERTION: Verify success card displays correct feedback
  const successCard = page.getByText('Application Submitted Successfully');
  await expect(successCard).toBeVisible();
});
```

#### Explanation
* **`selectOption('9')`**: Selects options by value attribute.
* **`expect().toHaveText()`**: Verifies that the step indicators update correctly on page transitions.
