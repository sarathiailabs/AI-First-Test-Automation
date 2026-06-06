# Day 10: Advanced UI Automation

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Dynamic Elements | 10 mins |
| Dynamic Tables | 15 mins |
| Pagination | 10 mins |
| Dynamic Dropdowns | 10 mins |
| Auto Suggestions | 10 mins |
| Search Results | 10 mins |
| Infinite Scroll | 10 mins |
| Lazy Loading | 10 mins |
| Multi-Step Forms | 15 mins |
| Downloads | 10 mins |
| Retry Mechanisms | 10 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Automate elements with dynamic classes, IDs, and text contents.
* Traverse, extract, and validate rows and columns inside dynamic data tables.
* Handle multi-page pagination lists and verify dynamic data reloading.
* Automate dynamic dropdown lists, input auto-suggestions, and search autocomplete results.
* Simulate infinite scrolling pages and synchronize tests with lazy-loaded content.
* Automate multi-step form wizard flows and verify step validations.
* Manage and assert secure file download payloads natively.
* Configure custom action retry limits and test retries to reduce pipeline flakiness.

---

## Introduction

This module covers advanced automation scenarios encountered in production web applications. We will study how to write timing-resistant test scripts to handle dynamically changing components, paginated elements, auto-suggestions, infinite scrolls, lazy loading, multi-page form wizards, file downloads, and configuration retries.

---

## Dynamic Elements

### Definition
**Dynamic Elements** are web components whose DOM attributes (such as IDs, class names, or text content) change dynamically on page refresh or after user actions. *(Aise elements jinke classes ya IDs page reload karne par automatically change hote rehte hain).*

### Key Concepts
* In modern frameworks (React/Angular), element attributes are often auto-generated (e.g. `id="button-a987d"`).
* Avoid using full dynamic values. Match static substrings using CSS attribute matchers (`^=` starts with, `*=` contains, `$=` ends with) or relative XPaths.
* Prefer using user-facing selectors like `getByRole()` or dedicated `data-testid` attributes.

### Syntax
```javascript
// CSS Attribute matchers
page.locator('button[id^="btn-"]') // Starts with
page.locator('button[id*="-submit-"]') // Contains
```

### Example
#### Code
```javascript
// Target a submit button whose dynamic ID is "btn-vjti-987abc"
const submitBtn = page.locator('button[id^="btn-vjti-"]');
await submitBtn.click();
```

### Common Mistakes
* **Using full dynamic IDs in selectors:** Writing `page.locator('#btn-vjti-987abc')` will fail on the next page reload because the ID refreshes to a different suffix (like `btn-vjti-123xyz`). Use stable substrings instead.

### Topic Summary
Dynamic elements must be targeted using stable substring matchers or dedicated test attributes, avoiding fragile auto-generated IDs.

---

## Dynamic Tables

### Definition
A **Dynamic Table** is a grid element (`<table>`) where the number of rows, columns, and cell values update dynamically based on API database queries or user filters. *(Data grids jinka raw records and cells values real-time API responses ke sath badalta rehta hai).*

### Key Concepts
* Locate specific rows by filtering by unique column text: `page.locator('tr').filter({ hasText: 'uniqueVal' })`.
* Extract cell values from specific columns using locator chaining: `row.locator('td').nth(columnIndex)`.
* Run assertions on count changes dynamically.

### Visual Explanation
Analogy: Railway station departure board.
```text
  [ DEPARTURE GRID ]
  Row 1: Train 1005 (Punjab Mail)  ──► Delay: 10 mins  ──► [ Status Check ]
  Row 2: Train 2008 (Deccan Queen) ──► Delay: On Time   ──► [ Status Check ]
```
To check Deccan Queen status, find the row containing "Deccan Queen" first, then locate the status column cell inside it.

### Example
#### Code
```javascript
// Find the row for student "Rahul" and extract their status from the 3rd column cell
const targetRow = page.locator('table#student-table tr').filter({ hasText: 'Rahul' });
const statusCell = targetRow.locator('td').nth(2); // 3rd column (0-indexed)
await expect(statusCell).toHaveText('Fees Paid');
```

### Topic Summary
Automate dynamic tables by locating rows using target text filters, then targeting cell values inside the row scope.

---

## Pagination

### Definition
**Pagination** is the web layout pattern that splits large datasets into separate sequential pages, providing navigation buttons (Next, Previous, Page Numbers) to load data in chunks. *(Large lists ko multi-pages me split karke load karne ka pagination navigation buttons system).*

### Key Concepts
* Verify table rows on the initial page.
* Locate and click the "Next" page button.
* Wait for the table contents to reload (wait for the old rows to detach or the page indicator to update).
* Verify new row values on the second page.

### Example
#### Code
```javascript
// Navigate pages: verify item, click next page, wait for update, verify next item
const firstItem = page.getByText('Student ID: 1001');
await expect(firstItem).toBeVisible();

// Click next page button
await page.getByRole('button', { name: 'Next' }).click();

// Wait for the first page item to disappear
await firstItem.waitFor({ state: 'detached' });

// Verify second page item loads
await expect(page.getByText('Student ID: 1015')).toBeVisible();
```

### Topic Summary
Pagination testing requires clicking page controllers and verifying that the page contents update correctly using state transitions.

---

## Dynamic Dropdowns

### Definition
A **Dynamic Dropdown** is a search input or selection field that displays matching options from a backend API as the user types. *(Aise search selection dropdowns jinke options values user ke key inputs ke sath dynamic load hote hain).*

### Key Concepts
* Enter characters into the input box.
* Wait for the options panel to load in the DOM tree.
* Locate and click the target option from the list.

### Example
#### Code
```javascript
// Type "Computer" inside the course selector and wait for options to appear
const courseInput = page.getByPlaceholder('Search Engineering Course');
await courseInput.fill('Computer');

// Wait for options container list to appear, then click target option
const optionItem = page.locator('.dropdown-option-item').filter({ hasText: 'Computer Networks' });
await optionItem.click();
```

### Topic Summary
Dynamic dropdowns are automated by filling the input field, waiting for the list wrapper, and selecting matching options.

---

## Auto Suggestions

### Definition
**Auto Suggestions** (Autocomplete) is a UI feature that displays matching search recommendations in a dropdown list below the input box as the user types. *(User ke typing input ke base par niche recommendations drop list select karne ka option).*

### Key Concepts
* Type characters inside the search input.
* **Caution:** Do not use `fill()` if the application requires physical keypress triggers to launch queries. Use `locator.pressSequentially()` instead.
* Wait for the suggestions panel to display.
* Press the down arrow key `ArrowDown` followed by `Enter`, or click the option directly.

### Example
#### Code
```javascript
// Search college: type sequentially to trigger autocomplete dropdown
const collegeInput = page.getByPlaceholder('Enter College Name');
await collegeInput.pressSequentially('VJTI', { delay: 100 });

// Wait for suggestion list pop-up and click option
const suggestionList = page.locator('.autocomplete-results');
await expect(suggestionList).toBeVisible();
await page.getByText('VJTI, Mumbai').click();
```

### Topic Summary
Use `pressSequentially()` to trigger search recommendations, wait for the suggestion panel to display, and select the target option.

---

## Search Results

### Definition
**Search Results** represents the dynamic list of matching cards, items, or rows displayed on the page after submitting a search query. *(Search button submit karne ke baad dynamically load hone wali lists).*

### Key Concepts
* Type search queries and submit (by clicking search icon or pressing Enter key).
* Wait for search loaders (spinners) to disappear.
* Assert that the result grid contains matching cards.
* Verify empty states if searching for missing terms.

### Example
#### Code
```javascript
// Input query, submit search, and assert matching cards are visible
await page.getByPlaceholder('Search Library').fill('Java Programming');
await page.getByRole('button', { name: 'Search' }).click();

// Wait for result grid cards count to match expectations
const bookCards = page.locator('.book-card');
await expect(bookCards).toHaveCount(3);
```

### Topic Summary
Assert search results by entering search terms, waiting for loading states to settle, and verifying card count matchers.

---

## Infinite Scroll

### Definition
**Infinite Scroll** is a web design pattern that appends new items to the bottom of the page automatically as the user scrolls down, avoiding pagination. *(Pagination ke bina window scroll down karne par next data items automatically append karne ka design).*

### Key Concepts
* Target the scrolling container or page viewport.
* Scroll down to the page footer: `page.evaluate(() => window.scrollTo(0, document.body.scrollHeight))`.
* Wait for new network requests to complete or check if the list count increases.
* Repeat scrolling until the required element loads.

### Example
#### Code
```javascript
// Scroll down to load more items in an infinite student list
const initialCount = await page.locator('.student-card').count();

// Scroll to bottom
await page.evaluate(() => window.scrollTo(0, document.body.scrollHeight));

// Wait for the count of cards to increase
await expect(page.locator('.student-card')).toHaveCount(initialCount + 10);
```

### Topic Summary
Infinite scroll uses page scroll actions dynamically, verifying that new list items are appended to the DOM.

---

## Lazy Loading

### Definition
**Lazy Loading** is a performance optimization technique that delays loading off-screen assets (like images or elements) until they enter the user's visible viewport. *(Staging performance improve karne ke liye items ko onscreen view zone me aane par hi load karna).*

### Key Concepts
* Images often use placeholders (`src="placeholder.png"` with real URL in `data-src`).
* When the image enters the viewport, JavaScript swaps attributes.
* In tests, scroll the element into view first, wait for the image attribute to swap, then run assertions.

### Example
#### Code
```javascript
// Scroll to lazy loaded image, wait for attribute updates
const lazyImage = page.locator('img.student-avatar').nth(5);
await lazyImage.scrollIntoViewIfNeeded();

// Wait for placeholder to be replaced by the real image source URL
await expect(lazyImage).toHaveAttribute('src', /.*student-profile-5.png/);
```

### Topic Summary
Lazy loading requires scrolling elements into the viewport first, then asserting that placeholder attributes update to real URLs.

---

## Multi-Step Forms

### Definition
A **Multi-Step Form** (Wizard) splits a complex form into separate sequential pages (steps) to simplify data entry. *(Bade forms ko sequential steps me divide karke navigate karne ka input wizard).*

### Key Concepts
* Navigate through steps sequentially.
* Fill inputs and click "Next" on each step.
* Assert that the progress bar indicator updates on each transition.
* Verify review summaries before final submission.

### Visual Explanation
```text
  [ STEP 1: Personal Info ] ──► Fill Name ──► Click Next
                                   │
  [ STEP 2: Upload Documents ] ◄───┘ ──► Set photo ──► Click Next
                                   │
  [ STEP 3: Review & Submit ] ◄────┘ ──► Verify name/photo summary ──► Click Submit
```

### Example
#### Code
```javascript
// Step 1: Fill details
await page.getByPlaceholder('Enter Name').fill('Rahul Verma');
await page.getByRole('button', { name: 'Next' }).click();

// Step 2: Assert step update, fill next field
await expect(page.locator('.step-indicator')).toHaveText('Step 2 of 3');
await page.getByLabel('Scholarship Class').selectOption('CS');
await page.getByRole('button', { name: 'Next' }).click();

// Step 3: Review and click final submit
await page.getByRole('button', { name: 'Submit Application' }).click();
await expect(page.getByText('Application ID:')).toBeVisible();
```

### Topic Summary
Multi-step forms require navigating wizard screens sequentially, asserting step indicators update, and verifying summary details before submitting.

---

## Downloads

### Definition
**Downloads** represents handling and saving files (PDFs, Excel sheets, images) triggered by clicking web download buttons. *(Browser trigger actions se files generate karke custom directory me save karne ka setup).*

### Key Concepts
* Playwright handles file downloads by listening for the download event using `page.waitForEvent('download')`.
* Wrap the download promise block alongside the click action.
* Save the file to a custom directory using the `download.saveAs(path)` method.

### Syntax
```javascript
const [download] = await Promise.all([
  page.waitForEvent('download'),
  page.locator('#download-btn').click()
]);

// Save file
await download.saveAs('local/path/file.pdf');
```

### Example
#### Code
```javascript
const path = require('path');

// Download college syllabus PDF
const [download] = await Promise.all([
  page.waitForEvent('download'),
  page.getByRole('link', { name: 'Download VJTI Syllabus' }).click()
]);

const destinationPath = path.join(__dirname, 'syllabus.pdf');
await download.saveAs(destinationPath);
```

### Common Mistakes
* **Expecting file downloads to save in the system Downloads folder automatically:** By default, Playwright saves downloads in temporary folders. You must use `download.saveAs()` to save files in your desired project directory.

### Topic Summary
Use `waitForEvent('download')` alongside click triggers to capture download payloads, saving them using `saveAs()`.

---

## Retry Mechanisms

### Definition
**Retry Mechanisms** are configuration settings that automatically re-run failed tests or actions to handle temporary network glitches or flakiness. *(Staging pipelines me flaky failures reduce karne ke liye failed runs ko automatic execute karne ka system).*

### Key Concepts
* **Test Retries:** Configured globally inside `playwright.config.ts` using the `retries` option.
* **Action Retries:** Playwright actions (clicks, fills) auto-retry checks internally for up to 30 seconds by default.
* **Conditional retries:** Re-running specific test blocks dynamically in code if exceptions occur.

### Example
#### Configuration
```typescript
// config file setup
import { defineConfig } from '@playwright/test';

export default defineConfig({
  retries: 2, // Re-runs failed tests up to 2 times automatically in CI
  use: {
    actionTimeout: 10000 // Retry finding elements for up to 10 seconds before failing
  }
});
```

### Common Mistakes
* **Using retries to hide architectural flakiness:** Retries are a safety net for minor network hiccups, not a fix for poorly written selectors or missing waiting logic. Always fix flaky locator code instead of relying on retries.

### Topic Summary
Configure test retries in `playwright.config.ts` to reduce pipeline failures caused by temporary network latency.

---

## Session Summary

### Key Takeaways
1. **Dynamic Matchers:** Target dynamic elements using stable substrings (e.g. `^=`) rather than fragile auto-generated values.
2. **Table Traversal:** Filter dynamic tables by unique row text, then query columns within the row scope.
3. **Event Coordination:** File downloads and autocomplete inputs require event-based listeners (like `waitForEvent('download')`) to synchronize execution.
4. **Wizard Paths:** Automate multi-step forms sequentially, verifying step indicators and review details.
5. **Pipeline Stability:** Global configuration retries handle temporary network hiccups in CI pipelines automatically.

### Important Interview Points
* **How do you automate a dynamic web table in Playwright?**
  * Locate the target row using `page.locator('tr').filter({ hasText: 'uniqueVal' })`. Then, locate columns inside the filtered row scope using `row.locator('td').nth(index)`.
* **How does Playwright handle file downloads?**
  * We use `page.waitForEvent('download')` in a `Promise.all()` block alongside the click action, then save the file using `download.saveAs(path)`.
* **What is the difference between pressSequentially() and fill() for autocomplete inputs?**
  * `fill()` clears and inserts text instantly, which might not trigger keypress events required by some autocomplete dropdowns. `pressSequentially()` simulates physical keypress delays, triggering suggestions reliably.
* **Where do you configure test retries?**
  * Configure them in the `retries` block inside `playwright.config.ts` (e.g., `retries: 2`).

### Quick Revision Sheet

| Advanced Concept | Playwright API / Strategy | Key Option / Code Pattern |
| --- | --- | --- |
| **Dynamic ID** | Substring selectors | `page.locator('button[id^="btn-"]')` |
| **Table Filtering** | row filter matching | `locator('tr').filter({ hasText: '...' })` |
| **Autocomplete** | physical key typing | `pressSequentially('text', { delay: 100 })` |
| **File Download** | download event capture | `context/page.waitForEvent('download')` |
| **Test Retries** | Config file settings | `defineConfig({ retries: 2 })` |
| **Infinite Scroll** | Scroll body evaluation | `window.scrollTo(0, document.body.scrollHeight)` |
