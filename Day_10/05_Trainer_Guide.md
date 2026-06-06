# Day 10: Advanced UI Automation – Trainer Teaching Guide

This guide is designed for instructors delivering the "Advanced UI Automation" session. It details the lecture scripts, engagement strategies, whiteboard sketches, live terminal commands, and student coaching tips.

---

## Session Opening

### Welcome Script
"Hello everyone! Welcome to Day 10. Today, we are covering advanced web automation patterns. We will learn how to write stable scripts for dynamic elements and tables where IDs and row orders shuffle on every refresh. We will look at pagination, autocomplete search suggestions, infinite scrolls, lazy loading, multi-step wizards, and file downloads. Finally, we will learn how to configure retries to make our test runs stable. Let's get started."

### Session Goal
By the end of today's 2-hour session, you will confidently handle dynamically rendering elements, extract and validate rows inside dynamic data tables, manage paginated elements, handle autocomplete suggestions, download files natively, and configure test retry limits.

### Motivation
Imagine running a test suite in your CI/CD pipeline. Everything passes locally, but fails in the pipeline because a dynamic table row takes 200 milliseconds longer to load or a network hiccup interrupts a file download. As an automation engineer, you must build scripts that handle these situations. Today, we will learn the industry standards to handle these advanced UI behaviors.

---

## 1. Dynamic Elements

### Trainer Introduction
"Dynamic elements are web elements whose classes, IDs, or text attributes change dynamically on reload. Modern JavaScript frameworks (React/Angular) auto-generate these attributes (e.g. `id="btn-987abc"`). We avoid using full dynamic values in our selectors, and instead target stable substrings using starts-with or contains matchers."

### Student Engagement Questions
1. Why do modern frontend frameworks generate dynamic IDs for elements?
2. What CSS attribute selector matcher checks if an attribute starts with a specific string? (The `^=` matcher).
3. Why should you avoid using dynamic ID values in your locators? (They change on page reload, breaking tests).

### Whiteboard Teaching
Draw dynamic ID replacement:
```text
  Auto-Generated ID:  id="btn-vjti-987abc" (Changes to id="btn-vjti-123xyz" on reload)
  
  Fragile CSS:        #btn-vjti-987abc (Breaks next run)
  
  Stable CSS:         button[id^="btn-vjti-"] (Starts with - Matches every time!)
```

### Teaching Flow
1. Define dynamic element attributes.
2. Review CSS attribute selector matchers (`^=`, `*=`, `$=`).
3. Discuss best practices (starts-with matchers, test IDs).

### Live Coding Demonstration
#### Step 1
Write a dynamic selector script snippet:
```typescript
const submitBtn = page.locator('button[id^="btn-vjti-"]');
await submitBtn.click();
```
#### Step 2
Explain how this selector successfully matches the button even if its suffix ID updates.

### Transition Script
"Dynamic elements often group together to build data grids. Let's look at how to handle Dynamic Tables next."

---

## 2. Dynamic Tables

### Trainer Introduction
"A dynamic table is a grid where rows and column values update dynamically based on database queries or filters. Row orders shuffle, meaning index-based selectors (like `tr:nth-child(1)`) are fragile. Instead, we locate rows using unique text filters, then locate cells inside the row scope."

### Student Engagement Questions
1. Why are row index selectors fragile in dynamic tables? (Row orders can shuffle based on database sorting).
2. How do you isolate a specific row dynamically? (Filter by unique text: `.filter({ hasText: '...' })`).

### Whiteboard Teaching
Draw table row scoping:
```text
  [ table#student-grid ]
     ├── tr (Amit Sharma)  ◄── filter({ hasText: 'Amit Sharma' })
     │     └── td.nth(0)   ──► (Roll No cell: 1002)
     └── tr (Rahul Verma)
```

### Teaching Flow
1. Define dynamic table structures.
2. Introduce row scoping using the `.filter({ hasText: ... })` API.
3. Show how to chain column index selections (`.nth()`) inside the row.

### Live Coding Demonstration
#### Step 1
Write a table filter script:
```typescript
const targetRow = page.locator('#student-grid tbody tr').filter({ hasText: 'Amit Sharma' });
const rollNo = targetRow.locator('td').nth(0);
await expect(rollNo).toHaveText('1002');
```
#### Step 2
Explain how this selector isolates the cell value reliably even if the row shuffles.

### Transition Script
"If a table contains hundreds of rows, it is split into multiple pages. Let's look at Pagination next."

---

## 3. Pagination

### Trainer Introduction
"Pagination splits large datasets into separate pages. To test pagination, we navigate pages, click the 'Next' button, wait for the table contents to reload, and verify new row values. The key is to wait for the old page rows to detach from the DOM to ensure page reloads complete."

### Student Engagement Questions
1. If you click the 'Next' page button and assert new page records immediately, what race condition occurs? (The assertion might run on page 1's data before page 2 loads).
2. How do you wait for page 1 elements to detach? (`await locator.waitFor({ state: 'detached' })`).

### Whiteboard Teaching
Draw pagination transition checkpoints:
```text
  [ Page 1 Data ] ──► click(Next Button) ──► wait for [ Page 1 Data ] to detach ──► assert [ Page 2 Data ] visible
```

### Teaching Flow
1. Define pagination layouts.
2. Explain page transition race conditions.
3. Introduce waiting for element detachment to confirm reloads.

### Live Coding Demonstration
#### Step 1
Write a pagination test script:
```typescript
const firstPageRow = page.locator('#student-grid tr').nth(0);
await page.locator('#next-btn').click();
await firstPageRow.waitFor({ state: 'detached' });
await expect(page.locator('#student-grid tr').nth(0)).toBeVisible();
```
#### Step 2
Explain how this ensures reloads complete before assertions run.

### Transition Script
"Search filters often update dropdown options dynamically. Let's look at Dynamic Dropdowns next."

---

## 4. Dynamic Dropdowns

### Trainer Introduction
"Dynamic dropdowns are selection fields that query APIs to display options dynamically as you type. To automate them, we fill the search input, wait for the dropdown options container to render, and select the target option from the list."

### Student Engagement Questions
1. How does a dynamic dropdown differ from a standard HTML select dropdown? (Standard select options are static in HTML; dynamic dropdown options are loaded dynamically via APIs).
2. Why must we wait for the options container to be visible before clicking options? (Options take time to load from backend APIs).

### Teaching Flow
1. Define dynamic dropdown fields.
2. Outline automation steps: input -> wait list -> click option.

### Live Coding Demonstration
#### Step 1
Write a dropdown selection snippet:
```typescript
await page.getByPlaceholder('Select Course').fill('Computer');
const dropdownList = page.locator('.dropdown-option-container');
await expect(dropdownList).toBeVisible();
await page.locator('.option-item').filter({ hasText: 'Computer Networks' }).click();
```
#### Step 2
Explain how this synchronizes dropdown clicks reliably.

### Transition Script
"If recommendations load as you type, it is called autocomplete. Let's look at Auto Suggestions next."

---

## 5. Auto Suggestions

### Trainer Introduction
"Auto suggestions (autocomplete) display search recommendations as you type. Some search inputs listen for physical keyboard events (like keypress/keyup delays). Since `fill()` enters text instantly, it can fail to trigger these listeners. We use `pressSequentially()` instead to simulate typing delays."

### Student Engagement Questions
1. Why does `locator.fill()` sometimes fail to trigger search recommendations? (It inserts text instantly, bypassing keypress listeners).
2. What API simulates typing delays key-by-key? (`pressSequentially()`).

### Whiteboard Teaching
Compare input strategies:
```text
  fill('VJTI'):
  [ Value updated in 0ms ] ──► No keyup event ──► Suggestions stay hidden.
  
  pressSequentially('VJTI', { delay: 100 }):
  Type V (100ms) ──► Type J (100ms) ──► Type T (100ms) ──► Type I ──► Suggestions list pops up!
```

### Teaching Flow
1. Define autocomplete auto-suggestions.
2. Contrast `fill()` vs. `pressSequentially()`.
3. Show how to configure typing delays.

### Live Coding Demonstration
#### Step 1
Write a search autocomplete snippet:
```typescript
await page.getByPlaceholder('Enter Name').pressSequentially('VJTI', { delay: 100 });
await expect(page.locator('.autocomplete-results')).toBeVisible();
await page.getByText('VJTI, Mumbai').click();
```
#### Step 2
Run this test to show suggestions appearing and being clicked.

### Transition Script
"Once search queries are submitted, we verify the results grid. Let's look at Search Results next."

---

## 6. Search Results

### Definition
**Search Results** represents the dynamic list of matching cards or rows displayed on the page after submitting a search query.

### Key Concepts
* Type search queries and submit.
* Wait for loading spinner states to settle.
* Assert that the result grid card count matches.

### Student Engagement Questions
1. Why must you wait for search loading spinners to disappear? (To prevent counting old results or checking empty states prematurely).

### Live Coding Demonstration
#### Step 1
Write a search result validation block:
```typescript
await page.getByPlaceholder('Search').fill('Java');
await page.keyboard.press('Enter');
await page.locator('.search-spinner').waitFor({ state: 'hidden' });
await expect(page.locator('.book-card')).toHaveCount(3);
```
#### Step 2
Explain how this ensures result counts are verified on refreshed lists.

### Transition Script
"If lists load continuously without pagination, it is called Infinite Scroll. Let's see how we automate this."

---

## 7. Infinite Scroll

### Trainer Introduction
"Infinite scroll appends new items automatically as the user scrolls down. To automate this, we scroll the page viewport down, wait for new network requests to complete, and verify that the list item count increases."

### Student Engagement Questions
1. How do you scroll to the bottom of the page in Playwright? (By executing a JavaScript scroll window script).
2. How do you verify that new elements loaded? (Assert that the count of elements has increased).

### Whiteboard Teaching
Draw infinite scroll loops:
```text
  Record initial count ──► Scroll down ──► Wait for count to increase ──► Repeat if needed
```

### Teaching Flow
1. Define infinite scroll layouts.
2. Show how to execute scroll scripts using `page.evaluate()`.
3. Write element count assertions.

### Live Coding Demonstration
#### Step 1
Write an infinite scroll script:
```typescript
const cards = page.locator('.student-card');
const initialCount = await cards.count();

// Scroll page to the bottom
await page.evaluate(() => window.scrollTo(0, document.body.scrollHeight));

// Assert count increases
await expect(cards).toHaveCount(initialCount + 10);
```
#### Step 2
Run this to show items appending dynamically on scrolling.

### Transition Script
"If elements only download resources when visible on screen, it is called Lazy Loading. Let's check this next."

---

## 8. Lazy Loading

### Trainer Introduction
"Lazy loading delays downloading off-screen elements until they enter the viewport. To test this, we scroll the element into view first, wait for the placeholder image attribute to swap to the real URL, and run assertions."

### Student Engagement Questions
1. Why do websites lazy-load images? (To improve initial page load speeds).
2. What helper scrolls an element into view? (`scrollIntoViewIfNeeded()`).

### Whiteboard Teaching
Draw attribute swap:
```html
  Before scroll: <img src="placeholder.png" data-src="real-profile.png" />
  
  After scroll:  <img src="real-profile.png" data-src="real-profile.png" /> (Swap completed!)
```

### Teaching Flow
1. Define lazy loading optimizations.
2. Explain the placeholder to real source attribute swap.
3. Show how to scroll and assert attribute changes.

### Live Coding Demonstration
#### Step 1
Write a lazy load test:
```typescript
const lazyImg = page.locator('img.student-avatar').nth(5);
await lazyImg.scrollIntoViewIfNeeded();
await expect(lazyImg).toHaveAttribute('src', /.*student-profile-5.png/);
```
#### Step 2
Explain how this ensures images load correctly.

### Transition Script
"Now, let's look at how we automate multi-step wizard form flows."

---

## 9. Multi-Step Forms

### Trainer Introduction
"Multi-step forms split complex forms into sequential screens. We automate them by filling fields and clicking 'Next' on each page, asserting that progress step indicators update on each transition."

### Student Engagement Questions
1. Why are multi-step wizards preferred over single-page forms? (They simplify data entry for complex forms).
2. How do you verify that you transitioned to the next step? (Assert that the progress indicator or page heading has updated).

### Teaching Flow
1. Define multi-step forms.
2. Outline sequential navigation and step validation.

### Live Coding Demonstration
#### Step 1
Write a multi-step wizard test:
```typescript
const indicator = page.locator('.step-indicator');
await expect(indicator).toHaveText('Step 1 of 3');

await page.locator('#parent-income').fill('450000');
await page.getByRole('button', { name: 'Next' }).click();

await expect(indicator).toHaveText('Step 2 of 3');
```
#### Step 2
Explain how this synchronizes tests across form transitions.

### Transition Script
"If a form button triggers a file download, we must capture it. Let's look at Downloads next."

---

## 10. Downloads

### Trainer Introduction
"To automate file downloads, we use `page.waitForEvent('download')` inside a `Promise.all()` block alongside the click action, then save the file to a custom directory using `download.saveAs(path)`."

### Student Engagement Questions
1. Why doesn't Playwright save downloads in the system Downloads folder automatically? (To isolate tests and prevent polluting the host system).
2. What event listener captures downloads? (`waitForEvent('download')`).

### Whiteboard Teaching
Draw download event capture:
```text
  Promise.all([
    page.waitForEvent('download'),     <── Listener
    page.locator('#download-btn').click() <── Action
  ]) ──► saves file using download.saveAs('syllabus.pdf')
```

### Teaching Flow
1. Explain file download events.
2. Introduce the `Promise.all()` wrapper.
3. Show how to save files using `saveAs()`.

### Live Coding Demonstration
#### Step 1
Write a file download test:
```typescript
import path from 'path';

test('Download Syllabus PDF', async ({ page }) => {
  await page.goto('/syllabus');
  
  const [download] = await Promise.all([
    page.waitForEvent('download'),
    page.locator('#download-cs-syllabus-btn').click()
  ]);
  
  const filePath = path.join(__dirname, 'syllabus.pdf');
  await download.saveAs(filePath);
});
```
#### Step 2
Show the downloaded file in the project workspace folder.

### Transition Script
"Finally, let's look at how we configure Retries to handle flaky tests."

---

## 11. Retry Mechanisms

### Trainer Introduction
"To handle temporary network latency or glitches, we configure retries in `playwright.config.ts`. Test retries automatically re-run failed tests, helping reduce flaky failures in CI pipelines."

### Student Engagement Questions
1. What is the difference between action retries and test retries? (Action retries run internally during actions; test retries re-run the entire test block on failure).
2. Why is relying on retries to pass pipelines risky? (It hides underlying flakiness or poorly written locators instead of fixing them).

### Whiteboard Teaching
List config retry keys:
```typescript
  export default defineConfig({
    retries: 2, // Re-runs failed tests up to 2 times
    use: {
      actionTimeout: 10000 // Retries finding elements for up to 10 seconds
    }
  });
```

### Teaching Flow
1. Define test retries vs action retries.
2. Configure retries in the config file.
3. Discuss the risks of hiding flaky code with retries.

### Live Coding Demonstration
#### Step 1
Open `playwright.config.ts` and set `retries: 2`.
#### Step 2
Discuss how this handles temporary network glitches in CI runs.

---

## Session Closing

### Session Summary
"Today we covered:
1. Dynamic elements starts-with CSS matchers.
2. Traversing dynamic tables using text filters.
3. Navigating pagination controls.
4. Handling auto-suggestions using `pressSequentially()`.
5. Infinite scrolls and lazy loading viewport actions.
6. Multi-step forms sequential workflows.
7. File downloads event handling.
8. Configuring test retries globally."

### Knowledge Check Questions
1. What selector matcher checks if an attribute starts with a value? (`^=`).
2. Why is `pressSequentially()` preferred for autocomplete fields? (It types key-by-key, triggering search listeners).
3. How do you wait for pagination reloads? (Wait for old page elements to detach).
4. How do you save a file download? (Use `download.saveAs(path)`).
5. Where do you configure test retries? (In `playwright.config.ts`).

### Assignment Introduction
"To practice these concepts, open `02_Assignments.md`. You will automate search filters, Dynamic table pagination, syllabus downloads, and multi-step scholarship forms. Complete these assignments to build confidence before the next module."

### Homework Guidance
* Complete the three assignments.
* Try running tests in UI mode and watch the download events capture.
* Inspect a lazy-loaded page and verify the image attributes swap on scroll.

### Next Session Preview
"In our next session, we will start API Testing. We will learn REST architecture, HTTP methods (GET, POST, PUT, DELETE), and how to write automated API tests natively in Playwright."
