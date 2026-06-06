# Day 8: UI Actions + Assertions

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Navigation | 5 mins |
| UI Actions: Click | 5 mins |
| UI Actions: Fill | 5 mins |
| UI Actions: Hover | 5 mins |
| Keyboard Actions | 10 mins |
| Mouse Actions | 10 mins |
| Drag & Drop | 5 mins |
| Scrolling | 5 mins |
| Auto Wait | 10 mins |
| Explicit Wait | 10 mins |
| Load States | 5 mins |
| Network Idle | 5 mins |
| Hard Assertions | 10 mins |
| Soft Assertions | 10 mins |
| Timeout Management | 10 mins |
| Flaky Test Reduction | 10 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Trigger page navigations and standard user interactions like click and fill.
* Perform advanced physical simulations like scrolling, hovering, drag-and-drop, and keypresses.
* Explain Playwright's actionability checks (Auto Wait) and customize timeouts.
* Sync test steps using load states, network idle checkpoints, and custom selectors.
* Assert page states using hard and soft validation checks to build robust test reports.

---

## Introduction

This module covers the core interactions and validation patterns in Playwright. We will study how to simulate browser actions (clicks, keyboard input, drag-and-drop) and how to coordinate timing using modern sync strategies and assertions.

---

## Navigation

### Definition
**Navigation** is the process of loading web pages, moving back/forward in history, or reloading browser URLs. *(Browser URL open karne, next/back pages par move karne ya refresh karne ka system).*

### Key Concepts
* `page.goto(url)`: Loads a target webpage. By default, it waits for the `load` event.
* `page.reload()`: Refreshes the current page.
* `page.goBack()` and `page.goForward()`: Navigates history.

### Syntax
```javascript
await page.goto('https://erp.vjti.ac.in/portal/login');
```

### Example
#### Code
```javascript
// Navigate to the VJTI fee payment screen
await page.goto('https://erp.vjti.ac.in/portal/fees');
```

### Common Mistakes
* **Hardcoding full absolute URLs on local environments:** Use relative URLs in test scripts and set the `baseURL` in your config file.

### Topic Summary
Navigations load and refresh page states inside a browser tab.

---

## Click

### Definition
**Click** is the simulated user action that triggers button presses, checkbox clicks, or link selections. *(Web buttons aur triggers par tap karne ka action).*

### Key Concepts
* Triggers mouse-down, mouse-up, and click events.
* Automatically performs **actionability checks** (auto-waits for the element to be visible, stable, enabled, and receiving pointer events) before clicking.
* Supports modifier keys (e.g. holding `Shift` or `Control`).

### Syntax
```javascript
await locator.click();
// Advanced click options
await locator.click({ button: 'right', clickCount: 2 });
```

### Example
#### Code
```javascript
// Perform a double click on the payment button
await page.getByRole('button', { name: 'Submit Application' }).click({ clickCount: 2 });
```

### Topic Summary
The click action interacts with buttons and links, using automatic pre-checks to prevent clicking disabled elements.

---

## Fill

### Definition
**Fill** is the action used to clear existing text and input new characters inside form elements. *(Text input boxes aur forms me clear karke data type karna).*

### Key Concepts
* Focuses the element, selects all existing text, deletes it, and enters the new value character-by-character.
* Auto-waits for the input element to be visible and editable.
* Best for text inputs, password fields, and textareas.

### Syntax
```javascript
await locator.fill('text to type');
```

### Example
#### Code
```javascript
// Clear and fill the student roll input box
await page.getByPlaceholder('Enter Roll Number').fill('1005');
```

### Topic Summary
`fill()` is the standard method for typing text into input fields because it clears pre-filled values and checks editability.

---

## Hover

### Definition
**Hover** is the action of moving the mouse pointer over a target element without clicking it, often used to reveal hidden menus or tooltips. *(Mouse cursor ko bina click kiye element ke upar hold karna).*

### Key Concepts
* Simulates physical pointer moves.
* Triggers CSS `:hover` states and JavaScript hover listeners.
* Auto-waits for the element to be visible and stable.

### Syntax
```javascript
await locator.hover();
```

### Example
#### Code
```javascript
// Hover over the profile icon to open the drop-down menu
await page.locator('#profile-menu-trigger').hover();
```

### Topic Summary
Hover simulates cursor positioning, allowing tests to trigger and interact with dynamic hover-reveal components.

---

## Keyboard Actions

### Definition
**Keyboard Actions** are commands used to simulate physical keypresses, shortcuts, and character inputs at the active cursor position. *(Keyboards keys, actions (press, down, up), aur combinations trigger karna).*

### Key Concepts
* `page.keyboard.type('text')`: Types characters sequentially (doesn't clear existing values).
* `page.keyboard.press('Key')`: Presses and releases a single key (e.g. `Enter`, `Backspace`).
* `page.keyboard.down('Key')` / `page.keyboard.up('Key')`: Simulates holding a modifier key.

### Syntax
```javascript
await page.keyboard.press('Enter');
await page.keyboard.press('Control+A');
```

### Example
#### Code
```javascript
// Focus input, type search text, and press Enter keys
await page.getByPlaceholder('Search Courses').focus();
await page.keyboard.type('Web Automation');
await page.keyboard.press('Enter');
```

### Common Mistakes
* **Using `keyboard.type` when `fill` is cleaner:** `fill` is safer because it clears existing text and validates input state. Use `keyboard.type` only when you need to trigger keyboard-down events.

### Topic Summary
Keyboard actions simulate physical keyboard events, which is useful for shortcuts, focus shifts, and inputs.

---

## Mouse Actions

### Definition
**Mouse Actions** are coordinate-based operations used to simulate raw pointer movements, clicks, drag pathways, and scroll wheels. *(Coordinate systems se custom pointer gestures control karna).*

### Key Concepts
* `page.mouse.move(x, y)`: Moves mouse to exact coordinates.
* `page.mouse.down()` / `page.mouse.up()`: Simulates pressing and releasing the left mouse button.
* `page.mouse.click(x, y)`: Clicks at an exact coordinate.

### Example
#### Code
```javascript
// Perform a mouse press and trace movement path
await page.mouse.move(100, 100);
await page.mouse.down();
await page.mouse.move(200, 200);
await page.mouse.up();
```

### Common Mistakes
* **Using absolute coordinates on responsive pages:** Coordinates change based on screen sizes and viewports. Use locator-bound mouse clicks instead of hardcoded coordinates whenever possible.

### Topic Summary
Mouse actions provide low-level pointer control, but should avoid absolute coordinates to prevent tests from breaking.

---

## Drag & Drop

### Definition
**Drag & Drop** is the interaction of picking up a draggable element, moving it to another container, and releasing it. *(Ek block/item ko select karke coordinate scale par drag karna aur click release karna).*

### Key Concepts
* Native helper: `locator.dragTo(targetLocator)`.
* Manual sequence: Hover source -> Mouse down -> Hover target -> Mouse up.

### Syntax
```javascript
await sourceLocator.dragTo(targetLocator);
```

### Example
#### Code
```javascript
// Drag a course card into the selected bucket
const sourceCard = page.locator('.course-item[data-id="CS-101"]');
const targetBucket = page.locator('.enrollment-bucket');

await sourceCard.dragTo(targetBucket);
```

### Topic Summary
`dragTo` simplifies canvas sorting and drag-and-drop operations in a single command.

---

## Scrolling

### Definition
**Scrolling** is the action of moving the visible window viewport vertically or horizontally to bring off-screen elements into view. *(Screen vertical ya horizontal scroll karke hide hue elements ko scroll panel me show karna).*

### Key Concepts
* **Auto Scroll:** Playwright actions (clicks, fills) automatically scroll elements into view before interacting.
* `locator.scrollIntoViewIfNeeded()`: Scrolls the page only if the element is not currently visible.
* `page.mouse.wheel(deltaX, deltaY)`: Simulates mouse wheel scrolling.

### Example
#### Code
```javascript
// Scroll down to check regulatory details in the VJTI footer
const footerNode = page.locator('footer.college-footer');
await footerNode.scrollIntoViewIfNeeded();
```

### Topic Summary
While Playwright auto-scrolls during interactions, explicit scroll methods help check page boundaries and footer nodes.

---

## Auto Wait

### Definition
**Auto Wait** is Playwright's built-in synchronization mechanism that performs actionability checks on an element before executing an action. *(Action call karne se pehle element ke visible, enabled, aur stable hone ka automatic check).*

### Key Concepts
* Before clicking or filling, Playwright checks if the element is:
  1. Attached to the DOM.
  2. **Visible** (not hidden or `display: none`).
  3. **Stable** (not moving due to CSS animations).
  4. **Enabled** (not disabled).
  5. **Receivable** (not covered by modal overlays).
* **Interview Tip:** This prevents **flaky tests** caused by network latency or slow renders, eliminating the need for arbitrary sleep statements (e.g. `sleep(5000)`).

### Visual Explanation
Analogy: Bus driver check.
```text
  SELENIUM model (No auto-wait):
  [ Test Code ] ── Click! ──► [ Element disabled ] ──► Crash! (ElementNotInteractable)

  PLAYWRIGHT model (Auto-wait):
  [ Test Code ] ── Click! ──► [ Auto-Wait Checks ] ──► (Is visible? Yes)
                                                       (Is stable? Yes)
                                                       (Is enabled? Yes) ──► Click!
```

### Real World Usage
Handles asynchronous network calls dynamically, avoiding test failures on slow staging environments.

### Topic Summary
Auto Wait performs actionability checks before actions, making UI automation more reliable without manual delays.

---

## Explicit Wait

### Definition
**Explicit Wait** is a manual synchronization command that halts test execution until a specific condition (such as element visibility or text changes) is met. *(Kisi specific requirement (element visibility, attribute change) ke trigger hone ka manual wait).*

### Key Concepts
* Used when auto-wait is insufficient (e.g., waiting for custom loading spinners to disappear).
* APIs:
  * `locator.waitFor({ state: 'visible' | 'hidden' | 'detached' })`
  * `page.waitForFunction(fn)`

### Syntax
```javascript
await locator.waitFor({ state: 'visible', timeout: 5000 });
```

### Example
#### Code
```javascript
// Wait for the payment loading circle to disappear from the page
const loadingSpinner = page.locator('#payment-progress-spinner');
await loadingSpinner.waitFor({ state: 'hidden' });
```

### Common Mistakes
* **Using `page.waitForTimeout()`:** Avoid using `page.waitForTimeout(5000)` (hard sleep). This slows down runs. Always wait for specific states instead.

### Topic Summary
Explicit wait targets specific DOM changes, preventing race conditions without slowing down tests.

---

## Load States

### Definition
**Load States** are browser navigation checkpoints (such as `load`, `domcontentloaded`, or `networkidle`) that indicate how much of the page resources have finished downloading. *(Webpage assets and resources (images, JS) download level check indicators).*

### Key Concepts
* **`load`**: The HTML is loaded and all scripts/images are fully downloaded. (This is Playwright's default wait for page navigation).
* **`domcontentloaded`**: HTML structure is parsed and DOM is ready, but external images and styles might still be downloading (Faster).
* **`networkidle`**: Stops waiting when there are no new network connections for at least 500 milliseconds (Slow, use with caution on pages with constant analytics polling).

### Syntax
```javascript
await page.waitForLoadState('networkidle');
```

### Example
#### Code
```javascript
// Wait for all assets and fonts to load before taking a screenshot
await page.goto('https://erp.vjti.ac.in/portal/login');
await page.waitForLoadState('load');
```

### Topic Summary
Load states monitor the browser's loading progress, helping synchronize page screenshots and dashboard renders.

---

## Network Idle

### Definition
**Network Idle** is a load state that waits until there are no active network requests (such as API calls or asset downloads) for at least 500 milliseconds. *(Browser me requests connections completely status code zero hone ka indicator).*

### Key Concepts
* Ensures all asynchronous background API calls have finished loading.
* Useful for complex dashboards that load multiple tables dynamically.
* **Caution:** If the application has a recurring background heartbeat API or analytical trackers running every 2 seconds, network idle will time out.

### Example
#### Code
```javascript
// Wait for dashboard tables data to load completely
await page.goto('https://erp.vjti.ac.in/dashboard');
await page.waitForLoadState('networkidle');
```

### Topic Summary
Network idle waits for background traffic to settle, but can cause timeouts on sites with persistent analytics polling.

---

## Hard Assertions

### Definition
**Hard Assertions** are standard checkpoints that stop test execution immediately if a check fails. *(Verification checkpoints jo fail hone par script ko usi waqt stop kar dete hain).*

### Key Concepts
* Standard behavior of the `expect()` assertion library in Playwright.
* Used for critical checkpoints (e.g. verifying the user is on the correct page before performing further actions).
* If a hard assertion fails, the remaining lines in the test block are skipped, and the test is marked as failed.

### Syntax
```javascript
await expect(locator).toBeVisible();
await expect(page).toHaveURL(/dashboard/);
```

### Example
#### Code
```javascript
// Critical validation: Stop test if login failed
await expect(page.getByText('Welcome, Student')).toBeVisible();
```

### Topic Summary
Hard assertions halt test execution immediately on failure, protecting tests from running when critical pre-requisites fail.

---

## Soft Assertions

### Definition
**Soft Assertions** are checkpoints that log failures but allow the test to continue executing, reporting all errors at the very end of the run. *(Wo checkpoints jo test process ko run hone dete hain aur issues ko end me summary sheet report karte hain).*

### Key Concepts
* Crucial for verifying cosmetic details (e.g., checking multiple cards, text labels, or layout margins in a single test).
* Prevents tests from stopping early due to minor styling bugs.
* **Syntax:** `expect.soft(locator)...`

### Syntax
```javascript
await expect.soft(locator).toHaveText('Required Text');
```

### Example
#### Code
```javascript
// Validate individual course details; continue testing even if one has a typo
const items = page.locator('.course-item');
await expect.soft(items.nth(0)).toContainText('Data Structures');
await expect.soft(items.nth(1)).toContainText('Web Automation');
await expect.soft(items.nth(2)).toContainText('Operating Systems');
```

### Topic Summary
Soft assertions gather multiple non-critical layout or label verification results without stopping the execution flow.

---

## Timeout Management

### Definition
**Timeout Management** is the customization of maximum wait times for tests, assertions, and element locator actions to prevent infinite hangs. *(Script aur checks ke terminate limit seconds set karna).*

### Key Concepts
* **Test Timeout:** The maximum time allowed for a single test block (Default: 30,000ms).
* **Navigation Timeout:** Max time for `page.goto()` (Default: 30,000ms).
* **Action Timeout:** Max wait time for finding elements and actionability checks (Default: no limit).
* **Assertion Timeout:** Max wait time for `expect()` checks to pass (Default: 5,000ms).

### Example
#### Code
```typescript
// Customize timeouts directly in code for slow endpoints:
await page.goto('https://erp.vjti.ac.in/reports', { timeout: 60000 });
await expect(page.locator('#report-table')).toBeVisible({ timeout: 10000 });
```

### Topic Summary
Timeout management configures safe time limits for tests and actions, preventing tests from hanging indefinitely.

---

## Flaky Test Reduction

### Definition
**Flaky Test Reduction** is the design practice of writing timing-resistant selectors, avoiding hard sleep timers, and using auto-waiting assertions to build reliable tests. *(Tests ko stable banane aur dynamic failures reduce karne ki strategies).*

### Key Concepts
* **Avoid hard sleeps:** Never use `await page.waitForTimeout(5000)`.
* **Use web-first assertions:** Always prefer locator assertions (like `expect(locator).toBeVisible()`) over raw evaluations (like `expect(await locator.isVisible()).toBe(true)`), as raw checks do not auto-retry.
* **State synchronization:** Wait for loader screens to disappear before interacting.

### Example
#### Flaky Code:
```javascript
await page.click('#search-btn');
await page.waitForTimeout(2000); // Flaky sleep
expect(await page.locator('.result-row').count()).toBe(3); // Does not retry!
```
#### Robust Code (Flake-free):
```javascript
await page.click('#search-btn');
// Auto-retries check up to 5 seconds dynamically:
await expect(page.locator('.result-row')).toHaveCount(3);
```

### Topic Summary
Eliminating hard sleep timers and utilizing auto-retrying locator assertions are key to reducing test flakiness.

---

## Session Summary

### Key Takeaways
1. **Physical Simulations:** Playwright supports navigation, clicks, fills, hovers, scroll gestures, and drag-and-drop operations out of the box.
2. **Auto-Waiting:** Actionability checks run automatically before actions, eliminating the need for sleep delays.
3. **Assertions Difference:** Hard assertions halt tests instantly on failure, while soft assertions log non-critical failures and let the test continue.
4. **Resiliency:** Use web-first assertions like `expect(locator).toBeVisible()` to write stable, timing-resistant scripts.

### Important Interview Points
* **What is the difference between hard and soft assertions?**
  * Hard assertions stop test execution immediately on failure. Soft assertions log the failure but let subsequent test steps run, reporting all failures at the end of the test.
* **Why should you avoid page.waitForTimeout() in automation?**
  * It pauses execution for a fixed duration regardless of how fast the page loads. This slows down test runs. Instead, wait dynamically for specific element states using explicit waits.
* **How does Playwright's auto-wait work?**
  * It performs actionability checks (verifying the element is attached, visible, stable, enabled, and clickable) before performing any action.

### Quick Revision Sheet

| Concept | API Syntax | Auto-retry? | When to Use |
| --- | --- | --- | --- |
| **Hard Assertion** | `await expect(loc).toBeVisible()` | **Yes (5s)** | Critical checkpoints (like verifying login success) |
| **Soft Assertion** | `await expect.soft(loc).toHaveText()` | **Yes (5s)** | Non-critical layout checks |
| **Explicit Wait** | `await loc.waitFor({ state: 'hidden' })` | N/A | Waiting for loader overlays to dismiss |
| **Scrolling** | `await loc.scrollIntoViewIfNeeded()` | N/A | Verifying footer links or loading off-screen nodes |
| **Drag & Drop** | `await loc.dragTo(target)` | N/A | Dragging items into container boxes |
