# Day 8: UI Actions + Assertions – Trainer Teaching Guide

This guide is designed for instructors delivering the "UI Actions + Assertions" session. It details the lecture scripts, engagement strategies, whiteboard sketches, live terminal commands, and student coaching tips.

---

## Session Opening

### Welcome Script
"Hello everyone! Welcome to Day 8. In our last session, we installed Playwright and mastered locators. We know how to point to elements on the page. Today, we are going to interact with them. We will click buttons, type text, hover over menus, drag-and-drop elements, and scroll viewports. More importantly, we will learn how to write assertions—both hard and soft—to verify that our application works correctly under test conditions."

### Session Goal
By the end of today's 2-hour session, you will be able to perform standard and advanced UI interactions, explain Playwright's auto-waiting actionability checks, configure explicit waits, and use hard and soft assertions to build reliable, flake-free test scripts.

### Motivation
In test automation, writing interactions is only half the battle. The other half is timing and verification. If you click a search button, and check the results before the server returns a response, your test fails. How do we synchronize our code with the page load dynamically? Today, we will learn how Playwright does this automatically under the hood, and how to verify page states securely.

---

## 1. Navigation

### Trainer Introduction
"Navigation is the first step of any test. We use `page.goto(url)` to load a web page. By default, Playwright waits for the browser to trigger its 'load' event (meaning all HTML, CSS, and scripts have finished downloading) before moving to the next test step."

### Student Engagement Questions
1. What happens if you try to click a button before the page finishes loading?
2. How do you refresh the page programmatically in Playwright?
3. What is the default timeout for `page.goto()`? (30 seconds).

### Whiteboard Teaching
Draw the navigation flow:
```text
  [ page.goto(url) ] ──► [ Request Sent ] ──► [ DOMContentLoaded ] ──► [ Load Event ] ──► [ Step 2 (Click/Fill) ]
```

### Teaching Flow
1. Define the `page.goto()` command.
2. Discuss reload, back, and forward navigations.
3. Explain that Playwright waits for the load event by default.

### Live Coding Demonstration
#### Step 1
Open a test file and write:
```typescript
import { test } from '@playwright/test';

test('Navigation Demo', async ({ page }) => {
  await page.goto('https://erp.vjti.ac.in/portal/login');
  await page.reload();
});
```
#### Step 2
Run the test and show the execution log verifying it navigated and reloaded.

### Topic Recap
* **Syntax:** `await page.goto('/path')`
* **Takeaway:** Playwright waits for the page load event automatically before proceeding.

### Transition Script
"Now that we've navigated to the page, let's learn how to interact with form elements using Click and Fill."

---

## 2. UI Actions: Click & Fill

### Trainer Introduction
"Click and Fill are the two most common UI actions. `click()` simulates a mouse click on an element, while `fill()` focuses an input, clears any existing text, and types the new value. Before performing either action, Playwright runs automatic actionability checks to make sure the element is ready."

### Student Engagement Questions
1. Why is `fill()` preferred over just typing characters?
2. What does 'actionability checks' mean in simple terms?
3. What happens if you click a button that is hidden behind a modal overlay?

### Whiteboard Teaching
Draw the actionability check pipeline:
```text
  [ fill('1005') ] ──► checks: Attached? ──► Visible? ──► Stable? ──► Enabled? ──► Editable? ──► Clear & Type
```

### Teaching Flow
1. Explain the `click()` API and its options (right-click, double-click).
2. Explain the `fill()` API and why it clears input values by default.
3. Detail the pre-action checks (visibility, editability).

### Live Coding Demonstration
#### Step 1
Write a basic login interaction snippet:
```typescript
await page.getByPlaceholder('Enter Roll Number').fill('1005');
await page.getByRole('button', { name: 'Submit' }).click();
```
#### Step 2
Explain how Playwright waits for the input to be editable before typing, and waits for the button to be enabled before clicking.

### Common Student Doubts
#### Student Question
"If I click a button, does Playwright wait for the next page to load?"
#### Trainer Response
"No. `click()` only triggers the click event. If the click triggers a page transition or navigation, you must use assertions (like `toHaveURL`) which wait for the new page dynamically."

### Transition Script
"Sometimes, we need to hover over menus to reveal elements before we can click them. Let's look at Hover actions next."

---

## 3. UI Actions: Hover

### Trainer Introduction
"Hover simulates moving the mouse cursor over a target element. This is common on modern pages for revealing tooltips, dropdown menus, or quick-action panels. Playwright moves the mouse cursor to the center of the element dynamically."

### Student Engagement Questions
1. Can you click a button that is hidden in HTML with `display: none`?
2. What physical action triggers a CSS `:hover` state?

### Whiteboard Teaching
Draw the hover-reveal action sequence:
```text
  1. Hover over parent card:  [ Parent Card ]  ◄── Mouse Cursor
  2. Element revealed:        [ Add Button ]   (style changed to display: block)
  3. Click revealed element:  [ Add Button ]   ──► click()
```

### Teaching Flow
1. Define the hover action.
2. Discuss CSS hover states.
3. Show how to hover over dropdown menus to reveal hidden click triggers.

### Live Coding Demonstration
#### Step 1
Write a hover-and-click script snippet:
```typescript
const menuTrigger = page.locator('#profile-menu-trigger');
await menuTrigger.hover();
await page.getByRole('link', { name: 'Sign Out' }).click();
```
#### Step 2
Explain that if we click "Sign Out" without hovering first, the test will fail because the link is invisible in the DOM.

### Transition Script
"For advanced interactions, we can also control keyboard shortcuts and keystrokes directly."

---

## 4. Keyboard Actions

### Trainer Introduction
"While `fill()` handles standard form entry, sometimes we need to simulate low-level keyboard events like pressing `Enter`, backspacing, or using shortcuts like `Control+A` and `Control+C`. Playwright provides the `page.keyboard` API to handle these events."

### Student Engagement Questions
1. How do you trigger a search if there is no search button, but pressing the Enter key on the keyboard works?
2. What is the difference between `page.keyboard.type()` and `locator.fill()`?

### Whiteboard Teaching
List keyboard APIs:
```text
  - page.keyboard.press('Key')       <── Press and release (e.g. 'Enter')
  - page.keyboard.type('Text')       <── Type text at cursor position (no clear)
  - page.keyboard.down('Modifier')   <── Hold key down (e.g. 'Shift')
```

### Teaching Flow
1. Contrast `fill()` vs `keyboard.type()`.
2. Explain `keyboard.press()` for shortcuts and special keys.
3. Detail how to hold modifier keys (Control, Shift).

### Live Coding Demonstration
#### Step 1
Write a script that focuses an input field and triggers a select-all deletion:
```typescript
await page.locator('#search-box').focus();
await page.keyboard.type('Operating Systems');
await page.keyboard.press('Control+A');
await page.keyboard.press('Backspace');
```
#### Step 2
Explain how this simulates physical keyboard actions step-by-step.

### Transition Script
"Just like the keyboard, we can also simulate raw mouse coordinate movements and drag gestures."

---

## 5. Mouse Actions

### Trainer Introduction
"For drawing canvas boards, sliders, or maps, we need direct pointer controls. The `page.mouse` API allows us to move the mouse cursor to exact coordinates and trigger raw mouse clicks."

### Student Engagement Questions
1. Why is using absolute screen coordinates (like clicking at x=500, y=300) risky?
2. What canvas-based elements have you seen in web applications?

### Whiteboard Teaching
Draw coordinate grid mapping:
```text
  (0,0) Top-Left
    ┌─────────────────────────┐
    │                         │
    │        (x=100, y=100)   │
    │                         │
    └─────────────────────────┘
```

### Teaching Flow
1. Introduce the coordinate system (top-left is 0,0).
2. Explain move, down, and up mouse events.
3. Emphasize that coordinate-based tests can break if the screen size or layout changes.

### Live Coding Demonstration
#### Step 1
Write a basic mouse movement script:
```typescript
await page.mouse.move(100, 100);
await page.mouse.down();
await page.mouse.move(200, 200);
await page.mouse.up();
```
#### Step 2
Discuss when this low-level API is useful versus standard element locator clicks.

### Transition Script
"A common use case for mouse drag operations is Drag and Drop. Let's look at Playwright's helper for this."

---

## 6. Drag & Drop

### Trainer Introduction
"To automate dragging an element and dropping it into a container, Playwright provides a simple helper: `sourceLocator.dragTo(targetLocator)`. Under the hood, this handles moving the mouse, pressing click, dragging, and releasing it."

### Student Engagement Questions
1. How would you automate drag-and-drop manually using mouse actions?
2. What is the benefit of using `dragTo()` over manual sequences?

### Whiteboard Teaching
Sketch the drag-and-drop sequence:
```text
  [ Source Element ] ── hover ──► mouse.down ──► move to [ Target Element ] ──► mouse.up
```

### Teaching Flow
1. Define drag-and-drop interaction.
2. Introduce the `dragTo()` API.
3. Detail how Playwright waits for visibility of both source and target before executing.

### Live Coding Demonstration
#### Step 1
Write a clean drag-and-drop test block:
```typescript
const sourceItem = page.locator('.drag-item');
const targetBox = page.locator('.drop-zone');
await sourceItem.dragTo(targetBox);
```
#### Step 2
Run this test on a sample drag-and-drop demo page.

### Transition Script
"Next, let's look at Scrolling, which is often required to bring elements into view."

---

## 7. Scrolling

### Trainer Introduction
"While Playwright automatically scrolls elements into view before clicking them, we sometimes need to scroll explicitly to verify footer links, trigger lazy loading, or automate infinite scroll pages."

### Student Engagement Questions
1. If a button is off-screen, do you need to scroll to it before calling `.click()` in Playwright? (No, Playwright scrolls automatically).
2. What is the role of `scrollIntoViewIfNeeded()`?

### Teaching Flow
1. Explain Playwright's automatic scrolling behavior.
2. Define the `scrollIntoViewIfNeeded()` API.
3. Explain coordinate-based wheel scrolling.

### Live Coding Demonstration
#### Step 1
Write a scroll test script:
```typescript
const footer = page.locator('footer.college-footer');
await footer.scrollIntoViewIfNeeded();
```
#### Step 2
Demonstrate wheel scrolling:
```typescript
await page.mouse.wheel(0, 500); // Scroll down 500 pixels
```

### Transition Script
"Now that we've mastered UI actions, let's look at the core of automation synchronization: Auto Wait and Explicit Wait."

---

## 8. Auto Wait & Explicit Wait

### Trainer Introduction
"Playwright's biggest strength is its built-in Auto Wait. Before executing any action, it runs actionability checks to ensure the element is visible, stable, and ready. But sometimes, auto-wait isn't enough (e.g. waiting for a loading screen to disappear). In those cases, we use Explicit Waits."

### Student Engagement Questions
1. Why does Selenium require `Thread.sleep()` or explicit driver waits so often?
2. What is a 'flaky' test?
3. How do you wait for a button to disappear from the page?

### Whiteboard Teaching
Draw the wait synchronization flow:
```text
  AUTO WAIT (Implicit):
  Runs behind the scenes for click(), fill(), etc.
  
  EXPLICIT WAIT (Manual):
  await locator.waitFor({ state: 'visible' | 'hidden' })
```

### Teaching Flow
1. Define Playwright's auto-wait checks.
2. Discuss why hard sleep timers (`waitForTimeout`) are discouraged.
3. Explain `waitFor()` for visible/hidden element states.

### Live Coding Demonstration
#### Step 1
Write an explicit wait block:
```typescript
const spinner = page.locator('#loading-spinner');
await spinner.waitFor({ state: 'hidden', timeout: 10000 });
```
#### Step 2
Explain that the test will wait dynamically for up to 10 seconds, but will proceed the millisecond the spinner disappears.

### Transition Script
"We can also wait for broad browser conditions like page Load States and Network Idle."

---

## 9. Load States & Network Idle

### Trainer Introduction
"Sometimes we need to wait for the page structure to be ready before proceeding. Playwright provides load state synchronizations, including the 'networkidle' state, which waits until there are no new network requests for 500ms."

### Student Engagement Questions
1. What is the difference between HTML being ready vs images/assets finishing download?
2. When would using `networkidle` cause a test to hang indefinitely?

### Whiteboard Teaching
Contrast the load states:
```text
  - domcontentloaded : HTML parsed, DOM tree built (fast).
  - load             : Everything loaded (scripts, CSS, images).
  - networkidle      : No new API calls or asset requests active for 500ms.
```

### Teaching Flow
1. Explain the three main load states.
2. Detail the risks of using `networkidle` on pages with persistent polling or tracking scripts.

### Live Coding Demonstration
#### Step 1
Write a load state sync snippet:
```typescript
await page.goto('https://erp.vjti.ac.in');
await page.waitForLoadState('networkidle');
```
#### Step 2
Discuss when this is useful vs using element-specific assertions.

### Transition Script
"Once the page states are synced, we perform validations. Let's compare Hard Assertions and Soft Assertions."

---

## 10. Hard Assertions vs Soft Assertions

### Trainer Introduction
"An assertion is a validation check. Playwright provides both Hard Assertions and Soft Assertions. Hard assertions halt the test immediately if they fail. Soft assertions log the failure but let the test continue running, reporting all failures at the end."

### Student Engagement Questions
1. If your login validation fails, should your test continue running? (No, use a hard assertion).
2. If one link in a footer list has a typo, should the test stop immediately? (No, use soft assertions to check the other links too).

### Whiteboard Teaching
Draw assertion execution paths:
```text
  Hard Assertion:
  Assert 1 (Pass) ──► Assert 2 (Fail) ──► [ STOP TEST ]
  
  Soft Assertion:
  Assert 1 (Pass) ──► Assert 2.soft (Fail) ──► Assert 3 (Pass) ──► [ END TEST: Mark Failed ]
```

### Teaching Flow
1. Define hard assertions (`expect()`).
2. Define soft assertions (`expect.soft()`).
3. Explain auto-retry assertions (up to 5 seconds by default).

### Live Coding Demonstration
#### Step 1
Write a test block combining both assertions:
```typescript
// Hard assertion (critical check)
await expect(page).toHaveURL(/dashboard/);

// Soft assertions (non-blocking checks)
await expect.soft(page.locator('#nav-title')).toHaveText('VJTI Student Portal');
await expect.soft(page.locator('#footer-email')).toContainText('vjti.ac.in');
```
#### Step 2
Run the test and show the generated report showing how soft failures are logged.

### Transition Script
"Finally, let's look at Timeout Management and how to eliminate test flakiness."

---

## 11. Timeout Management & Flaky Test Reduction

### Trainer Introduction
"To prevent tests from hanging forever, we customize timeouts for tests, actions, and assertions. Managing timeouts correctly is a key part of reducing test flakiness."

### Student Engagement Questions
1. What is the default timeout for a test block? (30 seconds).
2. What is the default timeout for an assertion check? (5 seconds).
3. Why do web-first assertions reduce flakiness?

### Whiteboard Teaching
List timeout hierarchy:
```text
  - Global Test Timeout:  30,000ms
  - Assertion Timeout:     5,000ms
  - Navigation Timeout:   30,000ms
```

### Teaching Flow
1. Explain the timeout hierarchy.
2. Outline flaky test reduction strategies.
3. Contrast raw evaluations with web-first assertions.

### Live Coding Demonstration
#### Step 1
Write a flaky non-retrying assertion:
```typescript
// Flaky: Evaluates immediately and fails if element takes 100ms to load
expect(await page.locator('#result').isVisible()).toBe(true);
```
#### Step 2
Rewrite it as a robust web-first assertion:
```typescript
// Robust: Auto-retries for up to 5 seconds
await expect(page.locator('#result')).toBeVisible();
```

---

## Session Closing

### Session Summary
"Today we covered:
1. Navigation and core UI actions (Click, Fill, Hover, Keyboard, Mouse, Drag-and-Drop, Scroll).
2. Playwright's Auto Wait actionability checks.
3. Explicit Waits and Load States.
4. The differences between Hard and Soft Assertions.
5. Customizing timeouts and writing web-first assertions to eliminate flakiness."

### Knowledge Check Questions
1. What is the difference between hard and soft assertions? (Hard stops test execution immediately; Soft logs the failure and continues).
2. Why is `page.waitForTimeout()` discouraged? (It introduces hard sleeps, making tests slow and flaky).
3. Name three actionability checks Playwright performs before clicking. (Attached, visible, stable, enabled, receiving events).
4. Which assertion auto-retries for up to 5 seconds: `expect(await loc.isVisible()).toBe(true)` or `await expect(loc).toBeVisible()`? (`await expect(loc).toBeVisible()`).
5. What is the default timeout for a test run? (30 seconds).

### Assignment Introduction
"To practice these concepts, open `02_Assignments.md`. You will automate logging into the VJTI portal, search the course registry using a mix of hard and soft assertions, and automate scroll and hover actions on a Kirana cart page. Complete these assignments to build confidence before the next module."

### Homework Guidance
* Complete the three assignments.
* Experiment with changing timeouts in `playwright.config.ts` and verify how they affect execution logs on failure.
* Automate a hover-reveal menu on a public site (like a header navigation menu) and click a link inside it.

### Next Session Preview
"In our next session, we will look at Forms, Windows, and Frames. We will learn how to automate dropdowns, checkboxes, radio buttons, deal with multiple browser tabs, and target elements inside nested iFrames."
