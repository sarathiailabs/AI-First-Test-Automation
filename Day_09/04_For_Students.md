# Day 9: Forms + Windows + Frames – Student Revision Notes

Quick reference guide to revise the concepts covered in the Forms + Windows + Frames session. Use this to review dropdown selections, checkbox states, file uploads, tab context events, iframes, and alert handlers.

---

## Forms

### Definition
A **Form** represents a group of input controls (inputs, dropdowns, checkboxes) wrapped inside a `<form>` element, used to submit user data. *(Web inputs ka container jo user details submit karne ke liye use kiya jata hai).*

### Important Syntax
```javascript
const form = page.locator('#my-form');
```

### Example
```javascript
await page.locator('#login-form').locator('input[name="username"]').fill('Rahul');
```

### Key Points
* Form elements can be automated individually or located relative to the parent form container.
* Submission can be triggered by clicking buttons with type `submit`.

### Common Mistakes
* **Assuming form elements load instantly:** Complex forms load dynamically. Wait for key inputs to be visible before filling.

---

## Dropdowns

### Definition
A **Dropdown** (HTML `<select>`) is a selector component that lets users pick one or more options from a collapsible list. *(Collapsible list jisme se options select kiye ja sakte hain).*

### Important Syntax
```javascript
await locator.selectOption('value');
await locator.selectOption({ label: 'Label Text' });
```

### Example
```javascript
await page.locator('#branch-select').selectOption({ label: 'Information Technology' });
```

### Key Points
* Automated using the native `selectOption()` API.
* Standard select boxes do not respond reliably to raw click actions.

### Common Mistakes
* **Trying to use `selectOption` on custom dropdowns:** If a dropdown is built using `<div>` and `<li>` elements, `selectOption` will fail. You must use click actions instead.

---

## Checkboxes

### Definition
A **Checkbox** is a binary selection box that lets users toggle options on or off. *(Ek box jise toggle (check/uncheck) kiya ja sakta hai).*

### Important Syntax
```javascript
await locator.check();
await locator.uncheck();
```

### Example
```javascript
await page.locator('#terms-checkbox').check();
```

### Key Points
* `check()` and `uncheck()` are idempotent.
* Use `toBeChecked()` to assert checkbox states.

### Common Mistakes
* **Using `click()` instead of `check()`:** Clicking an already checked box will uncheck it. Use `check()` to select checkboxes reliably.

---

## Radio Buttons

### Definition
A **Radio Button** is a mutual-exclusion selector that lets users select exactly one option from a pre-defined group. *(Multiple options me se kisi ek option ko choose karne ka round selector button).*

### Important Syntax
```javascript
await locator.check();
```

### Example
```javascript
await page.locator('input[type="radio"][value="male"]').check();
```

### Key Points
* Selecting one radio button automatically de-selects other radios in the same group.
* Assert states using `toBeChecked()`.

### Common Mistakes
* **Forgetting to check if the radio button is disabled:** Verify the button is enabled before calling `check()`.

---

## Date Pickers

### Definition
A **Date Picker** is an input element or calendar widget used to select dates, months, and years. *(Calender popup ya input field jahan date choose ki jati hai).*

### Important Syntax
```javascript
await locator.fill('YYYY-MM-DD');
```

### Example
```javascript
await page.locator('input[type="date"]').fill('2004-08-15');
```

### Key Points
* If the input allows text entry, prefer `fill()` (fastest and most stable).
* If the field is read-only, you must automate clicking the calendar widget elements.

### Common Mistakes
* **Using the wrong date format:** Date inputs on standard HTML forms expect dates formatted as `'YYYY-MM-DD'`. Passing other formats will cause validation failures.

---

## File Upload

### Definition
**File Upload** attaches local files to an `<input type="file">` element. *(Local files select karke input tag me load karna).*

### Important Syntax
```javascript
await locator.setInputFiles('path');
await locator.setInputFiles([]); // Clear upload
```

### Example
```javascript
await page.locator('#photo-upload').setInputFiles('mock-profile.png');
```

### Key Points
* `setInputFiles()` sets files directly on the input node, avoiding blocking OS dialog windows.
* Pass an empty array `[]` to clear selected files.

### Common Mistakes
* **Clicking the file input before setting files:** Clicking the file input opens the OS file selector dialog, which blocks test execution. Call `setInputFiles()` directly.

---

## Popups

### Definition
A **Popup** is a new browser window context opened dynamically when clicking links with `target="_blank"` or invoking `window.open()`. *(Naya tab ya window jo click or action ke baad pop-up hota hai).*

### Important Syntax
```javascript
const [popup] = await Promise.all([
  context.waitForEvent('page'),
  page.click('#popup-trigger')
]);
```

### Example
```javascript
const [newTab] = await Promise.all([
  context.waitForEvent('page'),
  page.locator('#pay-fees-btn').click()
]);
```

### Key Points
* New tabs belong to the same browser context, sharing cookies and session states.
* Wrap the tab capture in a `Promise.all()` block alongside the click action.

### Common Mistakes
* **Registering the event listener after clicking:** Register the listener before the click to prevent race conditions.

---

## Multiple Windows

### Definition
**Multiple Windows** represents managing and switching control between multiple active browser pages running inside the same automation context. *(Multiple open tabs ya windows me control focus switch karna).*

### Important Syntax
```javascript
const pages = context.pages();
```

### Example
```javascript
const allTabs = context.pages();
const secondTab = allTabs[1];
await secondTab.bringToFront();
```

### Key Points
* Retrieve all open tabs using `context.pages()`.
* Switch focus by running actions on the target page reference.

### Common Mistakes
* **Trying to use pages from a closed context:** Ensure the parent browser context remains open.

---

## Tabs

### Definition
A **Tab** is a single page view running inside a browser window. In Playwright, tabs are represented as Page objects. *(Browser window ka ek individual tab view).*

### Important Syntax
```javascript
const newTab = await context.newPage();
```

### Example
```javascript
const myNewTab = await context.newPage();
await myNewTab.goto('https://erp.vjti.ac.in');
```

### Key Points
* Playwright treats all tabs as Page objects under the same Browser Context.
* Closing a tab does not close the parent browser instance.

### Common Mistakes
* **Treating tabs as separate browsers:** Tabs share the same context, cookies, and local storage.

---

## Frames & iFrames

### Definition
An **iFrame** is an HTML element that embeds another independent HTML document inside the current web page. *(Webpage ke andar external webpage load karne wala frame).*

### Important Syntax
```javascript
const frame = page.frameLocator('iframe-selector');
```

### Example
```javascript
const paymentFrame = page.frameLocator('iframe#payment-gateway-frame');
await paymentFrame.locator('#card-num').fill('4111 2222 3333 4444');
```

### Key Points
* Elements inside an iFrame cannot be located using normal page locators directly.
* Use `page.frameLocator()` to access the frame first, then chain selectors.

### Common Mistakes
* **Locating iframe elements directly from the main page:** If you try to target an element inside an iframe using `page.locator('#btn')`, the search will fail and throw a timeout error.

---

## Alerts

### Definition
**Alerts** are native browser dialog windows (Alert, Confirm, Prompt) that display notifications or request inputs. *(Browser ke native alert pop-ups jo screen activity block karte hain).*

### Important Syntax
```javascript
page.on('dialog', async dialog => {
  await dialog.accept();
});
```

### Example
```javascript
page.on('dialog', async dialog => {
  console.log(dialog.message());
  await dialog.dismiss(); // Clicks Cancel
});
```

### Key Points
* Playwright automatically dismisses native dialogs by default so your test doesn't hang.
* Register a listener event using `page.on('dialog')` before triggering the dialog.

### Common Mistakes
* **Forgetting to register the listener beforehand:** If you click the trigger button first and then register the listener, the dialog will have already popped up and been auto-dismissed.

---

## Assignment Summary

* **Assignment 1: VJTI Student Registration Form Automation**
  * *Concepts Practiced:* Dropdown options selection, radio buttons and checkboxes toggling, and date picker inputs.
* **Assignment 2: Profile Picture File Upload Validation**
  * *Concepts Practiced:* Dynamic local file uploads using `setInputFiles()`, and clearing file states.
* **Assignment 3: Window, iFrame, & Alert Coordination**
  * *Concepts Practiced:* Tab context tracking, nested iFrame element lookups, and native dialog alert handling.

---

## Quick Revision Sheet

| Control Element | API / Method | Key Usage Scenario |
| :--- | :--- | :--- |
| **Dropdown** | `selectOption()` | Native select dropdown options |
| **Checkbox / Radio** | `check()` | Idempotent selection |
| **File Upload** | `setInputFiles()` | Direct local file attachments |
| **Iframe** | `frameLocator()` | Navigating inside nested sub-frames |
| **New Tab** | `context.waitForEvent('page')` | Handling popups and links with target="_blank" |
| **JS Alert** | `page.on('dialog', fn)` | Custom handling of native browser dialogs |

---

## Important Takeaways

1. **Idempotence:** Use `check()` instead of `click()` for checkboxes and radio buttons to prevent unchecking elements accidentally.
2. **Context Events:** Set up listeners like `waitForEvent('page')` or `on('dialog')` **before** performing the action that triggers them to prevent race conditions.
3. **Frame Isolation:** Always target iFrame elements using `frameLocator()` before looking up child elements inside the frame.
