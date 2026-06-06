# Day 8: UI Actions + Assertions – Student Revision Notes

Quick reference guide to revise the concepts covered in the UI Actions + Assertions session. Use this to review interaction syntax, synchronization rules, and assertion behaviors.

---

## Navigation

### Definition
**Navigation** consists of the commands used to load pages, refresh the screen, or step back/forward in the session history. *(Webpages open aur refresh karne ke actions).*

### Important Syntax
```javascript
await page.goto('url');
await page.reload();
```

### Example
```javascript
await page.goto('https://erp.vjti.ac.in/portal/login');
```

### Key Points
* `page.goto()` waits for the load state by default.
* Viewports and timeouts can be customized inside the navigation call.

### Common Mistakes
* **Assuming navigation is instant:** Staging pages might load slowly. Use assertions that support auto-retries on the next step.

---

## UI Actions: Click

### Definition
**Click** simulates clicking on elements (buttons, checkboxes, links) to trigger event handlers. *(Elements par click karne ka simple action).*

### Important Syntax
```javascript
await locator.click();
await locator.click({ clickCount: 2 }); // Double click
```

### Example
```javascript
await page.getByRole('button', { name: 'Submit' }).click();
```

### Key Points
* Triggers mouse-down, mouse-up, and click events.
* Performs actionability checks automatically before executing the click.

### Common Mistakes
* **Clicking an element covered by a loading spinner:** If a spinner covers the button, the click will fail or timeout. Wait for the spinner to disappear first.

---

## UI Actions: Fill

### Definition
**Fill** focuses an input field, clears any existing text, and types the new text characters. *(Inputs clear karke value type karna).*

### Important Syntax
```javascript
await locator.fill('value');
```

### Example
```javascript
await page.getByPlaceholder('Enter Roll Number').fill('1005');
```

### Key Points
* Clears out any default or pre-entered text before typing.
* Verifies that the input element is visible and editable.

### Common Mistakes
* **Using `fill()` on non-input elements:** `fill()` only works on `<input>`, `<textarea>`, or `[contenteditable]` elements.

---

## UI Actions: Hover

### Definition
**Hover** positions the mouse cursor directly over an element without clicking it, triggering hover menus or tooltips. *(Mouse pointer ko element ke upar hover hold karna).*

### Important Syntax
```javascript
await locator.hover();
```

### Example
```javascript
await page.locator('#profile-menu-trigger').hover();
```

### Key Points
* Simulates physical cursor placement.
* Triggers CSS `:hover` states and mouseover events.

### Common Mistakes
* **Hovering over off-screen elements without scrolling:** Playwright auto-scrolls, but if the element is hidden behind a fixed header, the hover might target the header instead.

---

## Keyboard Actions

### Definition
**Keyboard Actions** are commands used to simulate key presses, shortcuts, and character sequences. *(Keyboard inputs aur actions simulation).*

### Important Syntax
```javascript
await page.keyboard.press('Key');
await page.keyboard.type('text');
```

### Example
```javascript
await page.keyboard.press('Control+A');
await page.keyboard.press('Backspace');
```

### Key Points
* `press()` handles action combinations (like `Control+V`).
* `type()` types text at the cursor position without clearing existing text.

### Common Mistakes
* **Using `keyboard.type()` when `fill()` is better:** `type()` doesn't clear the field. Use it only when simulating physical key events (like triggering autocomplete dropdowns).

---

## Mouse Actions

### Definition
**Mouse Actions** are commands used to simulate low-level pointer gestures like moving, pressing, and releasing clicks on coordinate scales. *(Coordinates par custom mouse pointer control).*

### Important Syntax
```javascript
await page.mouse.move(x, y);
await page.mouse.down();
await page.mouse.up();
```

### Example
```javascript
await page.mouse.move(150, 150);
await page.mouse.down();
await page.mouse.up();
```

### Key Points
* Allows low-level coordinate-based operations.
* Useful for canvas drawings or sliders.

### Common Mistakes
* **Hardcoding absolute coordinates:** Screen viewports vary across devices, making absolute coordinates fragile. Use element relative offsets instead.

---

## Drag & Drop

### Definition
**Drag & Drop** selects a draggable element, drags it to a target locator container, and releases it. *(Drag component ko target drop-box me set karna).*

### Important Syntax
```javascript
await source.dragTo(target);
```

### Example
```javascript
await page.locator('.drag-card').dragTo(page.locator('.drop-zone'));
```

### Key Points
* Triggers mouse-down, hover target, and mouse-up in a single command.
* Auto-waits for both source and target visibility.

### Common Mistakes
* **Targeting invisible dropzones:** Ensure that the target drop container is visible before executing the drag-and-drop.

---

## Scrolling

### Definition
**Scrolling** moves the browser window viewport vertically or horizontally to display elements that are off-screen. *(Screen scroll karke off-screen nodes display area me lana).*

### Important Syntax
```javascript
await locator.scrollIntoViewIfNeeded();
```

### Example
```javascript
await page.locator('footer.college-footer').scrollIntoViewIfNeeded();
```

### Key Points
* Playwright auto-scrolls before performing interactions.
* Use explicit scroll commands for visual checks (like footers or lazy loading).

### Common Mistakes
* **Scrolling excessively on static pages:** Avoid writing redundant scroll commands if Playwright is already auto-scrolling during clicks.

---

## Auto Wait

### Definition
**Auto Wait** is Playwright's built-in synchronization that performs actionability checks (visible, enabled, stable) before executing actions. *(Actions se pehle element readiness checks).*

### Example
Playwright automatically waits for a button to be enabled before clicking it.

### Key Points
* Prevents flaky test execution.
* Eliminates the need for manual sleep timers.
* Verifies visibility, editability, and receiver layers.

### Common Mistakes
* **Adding manual sleep timers:** Do not write `sleep(5000)` calls. Let Playwright's auto-wait handle delays dynamically.

---

## Explicit Wait

### Definition
**Explicit Wait** halts test execution until a specific condition (like element visibility, detachment, or custom function results) is met. *(Kisi specific element status update ka manual wait).*

### Important Syntax
```javascript
await locator.waitFor({ state: 'visible' | 'hidden' });
```

### Example
```javascript
await page.locator('#loading-spinner').waitFor({ state: 'hidden' });
```

### Key Points
* Essential for waiting for page transitions to complete.
* Can wait for items to attach, detach, show, or hide.

### Common Mistakes
* **Using `page.waitForTimeout()`:** Avoid using hard sleep timers, as they slow down execution. Wait for specific element states instead.

---

## Load States

### Definition
**Load States** monitor browser loading checkpoints to track resource downloads. *(Page rendering assets loading status).*

### Important Syntax
```javascript
await page.waitForLoadState('load' | 'domcontentloaded');
```

### Example
```javascript
await page.waitForLoadState('load');
```

### Key Points
* `load`: All assets (HTML, images, JS) are fully loaded.
* `domcontentloaded`: HTML is parsed and the DOM is ready.

### Common Mistakes
* **Assuming the DOM is fully interactive on `domcontentloaded`:** JavaScript bundles might still be downloading. Use web-first assertions on target elements instead.

---

## Network Idle

### Definition
**Network Idle** is a load state that waits until there are no new network connections active for at least 500 milliseconds. *(Network traffic settle hone ka indicator).*

### Important Syntax
```javascript
await page.waitForLoadState('networkidle');
```

### Example
```javascript
await page.goto('/dashboard');
await page.waitForLoadState('networkidle');
```

### Key Points
* Useful for complex dashboards that load data via multiple API calls.

### Common Mistakes
* **Using networkidle on pages with constant polling traffic:** If a page polls an analytics server every 2 seconds, network idle will hang until it times out.

---

## Hard Assertions

### Definition
**Hard Assertions** are standard validation checks that stop test execution immediately if a check fails. *(Checkpoints jo failure par execution block kar dete hain).*

### Important Syntax
```javascript
await expect(locator).toBeVisible();
```

### Example
```javascript
await expect(page).toHaveTitle('VJTI Student Portal');
```

### Key Points
* Uses the default `expect()` assertion library.
* Stops execution instantly on failure, saving resources.
* Auto-retries the check for up to 5 seconds by default.

### Common Mistakes
* **Asserting variables without `await`:** Always write `await` before locator assertions (e.g., `await expect(loc).toBeVisible()`).

---

## Soft Assertions

### Definition
**Soft Assertions** log failures but allow the test to continue running subsequent steps, reporting all failures at the end of the test. *(Failure log karne wale checks jo run block nahi karte).*

### Important Syntax
```javascript
await expect.soft(locator).toHaveText('value');
```

### Example
```javascript
await expect.soft(courseCard.nth(0)).toContainText('Algorithms');
await expect.soft(courseCard.nth(1)).toContainText('Structures');
```

### Key Points
* Prevents tests from stopping early due to minor styling or text bugs.
* Crucial for dashboard verification and cosmetic checklists.

### Common Mistakes
* **Using soft assertions for critical flows:** Do not use soft assertions to verify login success. If login fails, subsequent steps will fail anyway, cluttering your logs.

---

## Timeout Management

### Definition
**Timeout Management** is the customization of maximum wait times for tests, assertions, and navigation actions to prevent tests from hanging indefinitely. *(Time limit limits define karna).*

### Example
```typescript
await page.goto('/reports', { timeout: 60000 });
```

### Key Points
* Test timeout: Max time for the entire test block (Default: 30s).
* Assertion timeout: Max time for `expect()` checks to pass (Default: 5s).

### Common Mistakes
* **Setting excessively long action timeouts globally:** A 60-second action timeout will cause failed tests to take 60 seconds to report, slowing down your pipeline.

---

## Flaky Test Reduction

### Definition
**Flaky Test Reduction** consists of writing stable test logic to prevent tests from failing randomly due to network delays. *(Dynamic failures check and stability improvements).*

### Example
Use locator assertions like `expect(locator).toHaveCount(3)` instead of raw evaluations like `expect(await locator.count()).toBe(3)`.

### Key Points
* Avoid hard sleep timers.
* Use auto-retrying web-first assertions.
* Wait for loading indicators to dismiss before executing actions.

### Common Mistakes
* **Using non-retrying assertion statements:** Statements like `expect(await locator.isVisible()).toBe(true)` evaluate immediately and do not auto-retry.

---

## Assignment Summary

* **Assignment 1: VJTI ERP Login Automation**
  * *Concepts Practiced:* Navigation, fill inputs, click submit, and hard url assertions.
* **Assignment 2: Course Registry Search (Soft & Hard Assertions)**
  * *Concepts Practiced:* Simulating keyboard enter keys, hard assertions on result counts, and soft assertions on card contents.
* **Assignment 3: Kirana Shop Hover & Scroll Interactions**
  * *Concepts Practiced:* Scrolling off-screen elements into view, hovering to reveal quick add buttons, and cart badge assertions.

---

## Quick Revision Sheet

| Concept | Action / Syntax | Wait Type | Stops Test on Failure? |
| :--- | :--- | :--- | :--- |
| **`click()`** | Clicks element | Auto-waits for actionability | N/A |
| **`fill()`** | Types input value | Auto-waits for editability | N/A |
| **`waitFor()`** | Wait for state | Explicit Wait | Yes (timeouts) |
| **`networkidle`** | Wait for traffic | Load state wait | Yes (timeouts) |
| **Hard Assertion** | `await expect(loc)...` | Auto-retry (5s) | **Yes** |
| **Soft Assertion** | `await expect.soft(loc)...` | Auto-retry (5s) | **No** |

---

## Important Takeaways

1. **Auto-Waiting Power:** Playwright's actionability checks handle element synchronization automatically, meaning you don't need to write manual delays.
2. **Assertion Strategy:** Use hard assertions for critical blocks (like logging in or loading the correct page) and soft assertions for non-blocking UI validations.
3. **Flake Prevention:** Never use `page.waitForTimeout()`. Rely on web-first assertions that auto-retry dynamically to keep tests fast and reliable.
