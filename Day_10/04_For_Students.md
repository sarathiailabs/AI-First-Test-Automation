# Day 10: Advanced UI Automation – Student Revision Notes

Quick reference guide to revise the concepts covered in the Advanced UI Automation session. Use this to review dynamic tables, autocomplete selectors, lazy loads, wizard navigations, file downloads, and test retry setups.

---

## Dynamic Elements

### Definition
**Dynamic Elements** are web elements whose attributes (IDs, classes, texts) change on page refresh. *(Aise elements jinke class aur ID attributes change hote rehte hain).*

### Important Syntax
```javascript
page.locator('button[id^="static-prefix-"]') // Starts with
```

### Example
```javascript
const dynamicButton = page.locator('button[id^="btn-vjti-"]');
```

### Key Points
* Auto-generated attributes (like `id="btn-987abc"`) break tests if selected directly.
* Match static substrings using starts-with (`^=`), contains (`*=`), or ends-with (`$=`) selectors.
* Prefer using user-facing selectors like `getByRole()` or dedicated `data-testid` attributes.

### Common Mistakes
* **Using full dynamic values in selectors:** Writing `#btn-vjti-987abc` will fail when the ID refreshes to a different suffix on next reload.

---

## Dynamic Tables

### Definition
A **Dynamic Table** is a grid element (`<table>`) where row counts, column orders, and cell values update dynamically. *(Tables jinka raw records list API call ke baad dynamic render hota hai).*

### Important Syntax
```javascript
page.locator('tr').filter({ hasText: 'uniqueText' })
```

### Example
```javascript
const row = page.locator('#student-table tr').filter({ hasText: 'Rahul Verma' });
const rollCell = row.locator('td').nth(0);
```

### Key Points
* Filter rows by unique text column values first.
* Once the row scope is isolated, locate target cells inside the row using column indexing.

### Common Mistakes
* **Using index-based row selectors:** Avoid `tr:nth-child(1)` selectors, as row orders shuffle dynamically.

---

## Pagination

### Definition
**Pagination** splits large datasets into separate pages, using Next/Previous controllers to navigate page blocks. *(Large grids ko multi-pages navigation controls me split karne ka pattern).*

### Important Syntax
```javascript
await page.locator('#next-page-btn').click();
```

### Example
```javascript
await page.getByRole('button', { name: 'Next' }).click();
await firstRow.waitFor({ state: 'detached' }); // Wait for reload
```

### Key Points
* Click pagination buttons to navigate.
* Wait for the old page data rows to detach from the DOM to ensure page reloads complete before running assertions on the next page.

### Common Mistakes
* **Running assertions too early:** If you assert new page details immediately after clicking "Next", the check might run on the old page's data before it refreshes. Wait for the reload to finish first.

---

## Dynamic Dropdowns

### Definition
A **Dynamic Dropdown** is a select input that queries backend APIs to display options dynamically as you type. *(Aise selection dropdowns jinke option options user inputs ke base par API se fetch hote hain).*

### Important Syntax
```javascript
await locator.fill('query');
```

### Example
```javascript
await page.getByPlaceholder('Select Course').fill('Computer');
await page.locator('.dropdown-option').filter({ hasText: 'Computer Networks' }).click();
```

### Key Points
* Fill in the search box.
* Wait for the dropdown options container to render in the DOM tree.
* Locate and click the target option from the list.

### Common Mistakes
* **Assuming all dropdown options load instantly:** Network latency can delay options loading. Always wait for options to be visible before clicking.

---

## Auto Suggestions

### Definition
**Auto Suggestions** (Autocomplete) displays matching search recommendations in a dropdown list as you type. *(User key inputs ke text base par autocomplete recommendations list show karna).*

### Important Syntax
```javascript
await locator.pressSequentially('text', { delay: 100 });
```

### Example
```javascript
await page.getByPlaceholder('Enter VJTI Name').pressSequentially('VJTI', { delay: 100 });
await page.getByText('VJTI Campus').click();
```

### Key Points
* Use `pressSequentially()` to simulate realistic keypress delays, which triggers autocomplete listeners.
* Wait for the suggestion dropdown to display.

### Common Mistakes
* **Using `fill()` for autocomplete fields:** `fill()` enters text instantly, which might not trigger keypress events required by some autocomplete dropdowns. Use `pressSequentially()` instead.

---

## Search Results

### Definition
**Search Results** represents the matching items or cards displayed after submitting a search query. *(Search button submit karne ke baad load hone wali listings).*

### Important Syntax
```javascript
await expect(page.locator('.result-card')).toHaveCount(count);
```

### Example
```javascript
await page.getByPlaceholder('Search').fill('Java');
await page.keyboard.press('Enter');
await expect(page.locator('.book-card')).toHaveCount(3);
```

### Key Points
* Type queries and submit (by clicking Search or pressing Enter).
* Wait for loading spinner states to settle.
* Assert that the result count matches.

### Common Mistakes
* **Asserting counts immediately:** Always wait for loading spinners to disappear before checking results.

---

## Infinite Scroll

### Definition
**Infinite Scroll** appends new items to the bottom of the page automatically as you scroll down, avoiding pagination controls. *(Scroll down karne par list items automatically page bottom pe load ho jana).*

### Important Syntax
```javascript
await page.evaluate(() => window.scrollTo(0, document.body.scrollHeight));
```

### Example
```javascript
await page.evaluate(() => window.scrollTo(0, document.body.scrollHeight));
await expect(page.locator('.card')).toHaveCount(newCount);
```

### Key Points
* Scroll the page or scrollable container to the bottom.
* Wait for new network requests to complete.
* Assert that the element count has increased.

### Common Mistakes
* **Scrolling infinitely without an end condition:** Define expected list counts or scroll loops to prevent infinite loops in your tests.

---

## Lazy Loading

### Definition
**Lazy Loading** is a performance optimization that delays loading off-screen elements until they enter the viewport. *(Elements ko view region me aane par hi load karna).*

### Important Syntax
```javascript
await locator.scrollIntoViewIfNeeded();
```

### Example
```javascript
const lazyImage = page.locator('img.avatar').nth(5);
await lazyImage.scrollIntoViewIfNeeded();
await expect(lazyImage).toHaveAttribute('src', /.*avatar.png/);
```

### Key Points
* Off-screen lazy-loaded elements display placeholder attributes initially.
* Scroll the element into view first to trigger the load.
* Assert that the placeholder attribute swaps to the real asset URL.

### Common Mistakes
* **Asserting attributes without scrolling first:** The test will fail because lazy-loaded elements will display placeholder sources until scrolled into view.

---

## Multi-Step Forms

### Definition
A **Multi-Step Form** splits a complex form into separate sequential pages (steps) to simplify data entry. *(Bade forms ko sequential screens me divide karke navigate karne ka input wizard).*

### Important Syntax
```javascript
await page.getByRole('button', { name: 'Next' }).click();
```

### Example
```javascript
await page.locator('#parent-income').fill('450000');
await page.getByRole('button', { name: 'Next' }).click();
await expect(page.locator('.step-indicator')).toHaveText('Step 2 of 3');
```

### Key Points
* Navigate through the steps sequentially.
* Assert that progress indicators update on each page transition.
* Verify review summary screens before final submission.

### Common Mistakes
* **Skipping validations on intermediate steps:** Always verify page indicators to ensure transitions are successful.

---

## Downloads

### Definition
**Downloads** represents handling and saving files triggered by clicking download buttons. *(Actions triggers dwara files download karke custom local folder me save karna).*

### Important Syntax
```javascript
const [download] = await Promise.all([
  page.waitForEvent('download'),
  page.locator('#download-btn').click()
]);
await download.saveAs('path');
```

### Example
```javascript
const [download] = await Promise.all([
  page.waitForEvent('download'),
  page.locator('#syllabus-download').click()
]);
await download.saveAs('downloads/syllabus.pdf');
```

### Key Points
* Playwright handles file downloads by listening for the download event using `page.waitForEvent('download')`.
* Wrap the download promise block alongside the click action.
* Save the file to a custom directory using the `download.saveAs(path)` method.

### Common Mistakes
* **Expecting downloads to save in the system Downloads folder automatically:** By default, Playwright saves downloads in temporary folders. You must use `download.saveAs()` to save files in your desired project directory.

---

## Retry Mechanisms

### Definition
**Retry Mechanisms** are configuration settings that automatically re-run failed tests or actions to handle temporary network glitches. *(Exceptions and failures par failed tests runs auto-restart karne ki settings).*

### Example
```typescript
// Config setup in playwright.config.ts
export default defineConfig({
  retries: 2,
  use: { actionTimeout: 10000 }
});
```

### Key Points
* Test retries: Re-run failed tests globally (configured in `playwright.config.ts`).
* Action retries: Auto-wait retry checks run internally for up to 30 seconds.

### Common Mistakes
* **Using retries to hide architectural flakiness:** Retries are a safety net for minor network hiccups. Always fix fragile locator code instead of relying on retries to pass the pipeline.

---

## Assignment Summary

* **Assignment 1: VJTI Student Registry Dynamic Table & Pagination**
  * *Concepts Practiced:* Row text filtering, column cell indexing, next buttons click, and waiting for elements to detach.
* **Assignment 2: Syllabus PDF Download & Validation**
  * *Concepts Practiced:* File download event capture, local folder saves, and verifying file existence.
* **Assignment 3: Multi-Step Scholarship Application Form**
  * *Concepts Practiced:* Progress step indicator checks, input filling, dropdown choices, and wizard submit actions.

---

## Quick Revision Sheet

| Advanced Topic | Key API API / Selector | Synchronization Strategy |
| :--- | :--- | :--- |
| **Dynamic Table** | `.filter({ hasText: '...' })` | Filter rows by unique text, then index columns |
| **Pagination** | `await oldRow.waitFor({ state: 'detached' })` | Wait for old row detachment to verify page reload |
| **Auto Suggest** | `pressSequentially()` | Simulate realistic keypress delays |
| **Downloads** | `waitForEvent('download')` | Wrap listener and click in `Promise.all()` |
| **Infinite Scroll** | `window.scrollTo()` | Scroll down and wait for element count to increase |

---

## Important Takeaways

1. **Table Scoping:** Always isolate dynamic table rows using unique text filters before locating cell indices inside the row.
2. **Download Handling:** File downloads require event-based listeners (`waitForEvent('download')`) wrapped in a promise block alongside click actions.
3. **Flake-Free Autocomplete:** Use `pressSequentially()` to simulate key-by-key typing, which triggers autocomplete suggestion dropdowns reliably.
