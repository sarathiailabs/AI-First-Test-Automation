# Day 9: Forms + Windows + Frames

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Forms | 10 mins |
| Dropdowns | 10 mins |
| Checkboxes | 10 mins |
| Radio Buttons | 10 mins |
| Date Pickers | 15 mins |
| File Upload | 15 mins |
| Popups & Tabs | 15 mins |
| Multiple Windows | 10 mins |
| Frames & iFrames | 15 mins |
| JavaScript Alerts | 10 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Automate standard web form elements including text fields, selectors, dropdown options, and date calendars.
* Handle binary file uploads securely using Playwright's file APIs.
* Manage multiple browser windows, popups, and concurrent tab contexts.
* Locate and interact with elements nested inside isolated iFrames and sub-frames.
* Handle native browser JavaScript dialogues (alerts, confirms, prompts) by configuring listeners.

---

## Introduction

This module covers complex page layouts, forms, and browser contexts. We will study how to interact with form controls (dropdowns, checkboxes, radios, calendars, file inputs) and how to coordinate tests across multiple windows, tabs, iframes, and native dialogues.

---

## Forms

### Definition
A **Form** is a container element (`<form>`) used to group input controls (inputs, dropdowns, checkboxes) and transmit user data to a server. *(Web elements ka group jo user details collect karke database server par submit karta hai).*

### Key Concepts
* Form inputs are filled individually using Playwright actions.
* Form submission is triggered by clicking the submit button or pressing Enter on an input.

### Example
#### Code
```javascript
// Locate form container, fill fields, and submit
const registrationForm = page.locator('#registration-form');
await registrationForm.locator('input[name="studentName"]').fill('Rahul Verma');
await page.getByRole('button', { name: 'Submit' }).click();
```

### Topic Summary
Forms group input controls, which are automated by interacting with individual elements and triggering submit clicks.

---

## Dropdowns

### Definition
A **Dropdown** (HTML `<select>`) is a selector component that lets users pick one or more choices from a collapsible list of options. *(Collapsible options list jisme se user choice select karta hai).*

### Key Concepts
* Automated using the `locator.selectOption()` API.
* Can select options by visible text, raw value attribute, or index.
* For multi-select dropdowns, pass an array of values: `selectOption(['val1', 'val2'])`.

### Syntax
```javascript
await locator.selectOption('optionValue');
await locator.selectOption({ label: 'Visible Text' });
await locator.selectOption({ index: 2 });
```

### Example
#### Code
```javascript
// Select Computer Science engineering branch by visible label text
await page.getByLabel('Engineering Branch').selectOption({ label: 'Computer Science' });
```

### Common Mistakes
* **Using `click()` on option elements directly:** Standard select boxes do not respond reliably to raw clicks on `<option>` tags. Always use the `selectOption()` API.

### Topic Summary
`selectOption()` selects items in standard dropdowns by label, value, or index, avoiding raw clicks.

---

## Checkboxes

### Definition
A **Checkbox** is a binary selection box that lets users toggle options (such as terms and conditions or list items) on or off. *(Ek box jise toggle (check/uncheck) kiya ja sakta hai).*

### Key Concepts
* Playwright provides explicit APIs: `locator.check()` and `locator.uncheck()`.
* These methods are **idempotent**: calling `.check()` on an already checked box does nothing, whereas a raw `.click()` would uncheck it.
* Assert states using `await expect(locator).toBeChecked()`.

### Example
#### Code
```javascript
// Check the VJTI hostel agreement and verify it is checked
const agreeCheck = page.getByLabel('I agree to the VJTI Hostel Rules');
await agreeCheck.check();
await expect(agreeCheck).toBeChecked();
```

### Topic Summary
Use `check()` and `uncheck()` to interact with checkboxes reliably, and `toBeChecked()` to assert their states.

---

## Radio Buttons

### Definition
A **Radio Button** is a mutual-exclusion selector that lets users select exactly one option from a pre-defined group. *(Multiple options me se kisi ek option ko choose karne ka round selector button).*

### Key Concepts
* Share the same `name` attribute in HTML to group options together.
* Automated using the `locator.check()` API.
* Selecting one radio button automatically de-selects other radios in the same group.

### Example
#### Code
```javascript
// Select Male radio button and verify selection
const maleRadio = page.getByRole('radio', { name: 'Male' });
await maleRadio.check();
await expect(maleRadio).toBeChecked();
```

### Topic Summary
Radio buttons allow single choices within a group, using `check()` to select options and `toBeChecked()` to assert states.

---

## Date Pickers

### Definition
A **Date Picker** is an input element or calendar widget used to select dates, months, and years. *(Calender popup ya input field jahan date choose ki jati hai).*

### Key Concepts
* **Direct Input:** If the input allows text entry, use `locator.fill('DD-MM-YYYY')` (fastest and most stable).
* **Calendar UI:** If the field is read-only, you must automate clicking the calendar trigger, selecting the month/year navigation buttons, and clicking the target day cell.

### Example
#### Code
```javascript
// Automating direct input birthdate fill:
await page.getByLabel('Date of Birth').fill('2004-05-15');
```

### Common Mistakes
* **Trying to write text inside read-only fields:** If the element has a `readonly` attribute, `.fill()` will throw an editability error. You must click the calendar UI or remove the attribute using JavaScript.

### Topic Summary
Prefer direct input fills for date pickers. If read-only, automate clicks on the calendar widget elements.

---

## File Upload

### Definition
**File Upload** is the action of selecting local files and attaching them to an `<input type="file">` element to submit to the server. *(Local disk se files select karke web application page par load karna).*

### Key Concepts
* Playwright uses the `locator.setInputFiles()` API to set local files.
* **Important:** This does not click the "Browse" button (which opens native OS file dialogues that automation tools cannot control). It sets the file paths directly on the input node.
* To clear selected files, pass an empty array: `setInputFiles([])`.

### Syntax
```javascript
await page.locator('input[type="file"]').setInputFiles('filePath');
// Multiple files
await page.locator('input[type="file"]').setInputFiles(['file1.png', 'file2.png']);
```

### Example
#### Code
```javascript
const path = require('path');

// Upload student photo PDF dynamically from local folder
const filePath = path.join(__dirname, 'student-photo.png');
await page.locator('input[type="file"]').setInputFiles(filePath);
```

### Common Mistakes
* **Clicking the file input before setting files:** Clicking the file input opens the OS file selector dialog, which blocks test execution. Call `setInputFiles()` directly without clicking.

### Topic Summary
`setInputFiles()` uploads files directly on the file input element, bypassing blocking OS dialog windows.

---

## Popups & Tabs

### Definition
A **Popup** or **Tab** is a new browser window context opened dynamically when clicking links with `target="_blank"` or invoking `window.open()` in JavaScript. *(Naya tab ya window jo click or action ke baad pop-up hota hai).*

### Key Concepts
* In Playwright, new tabs belong to the same **Browser Context**, meaning they share session cookies automatically.
* You must wait for the new page event to trigger using `context.waitForEvent('page')`.

### Visual Explanation
Analogy: Opening a new book.
```text
  [ Browser Context (Lab Room) ]
      ├── [ Tab 1 (Page: Student ERP) ] ──► clicks "Pay Online"
      └── [ Tab 2 (Page: Bank Gateway) ] <── (New page event captured dynamically)
```

### Example
#### Code
```javascript
// Click button that opens a new tab and capture the page instance:
const [newTabPage] = await Promise.all([
  context.waitForEvent('page'),
  page.locator('#pay-online-btn').click() // Triggers window.open()
]);

// Control the new tab
await newTabPage.waitForLoadState('load');
await expect(newTabPage).toHaveTitle(/Secure Bank Gateway/);
```

### Common Mistakes
* **Waiting for the page after clicking:** If you click first and then write `waitForEvent()`, a race condition can occur if the tab loads faster than the listener registers. Always wrap them in `Promise.all()`.

### Topic Summary
Capture new browser tabs using `context.waitForEvent('page')` alongside click actions wrapped in a promise block.

---

## Multiple Windows

### Definition
**Multiple Windows** represents managing and switching control between multiple active browser pages/tabs running inside the same automation context. *(Multiple open tabs ya windows me control focus switch karna).*

### Key Concepts
* Retrieve all open tabs using `context.pages()`.
* Switch focus simply by running actions on the target page object reference (e.g. `await page2.click(...)`).

### Example
#### Code
```javascript
// Get all open pages inside the browser context
const allPages = context.pages();
console.log(`Number of active tabs: ${allPages.length}`);

// Switch focus and close the second tab
const secondTab = allPages[1];
await secondTab.close();
```

### Topic Summary
Retrieve active pages using `context.pages()`, allowing tests to interact with and close specific tabs.

---

## Frames & iFrames

### Definition
An **iFrame** (Inline Frame) is an HTML element that embeds another independent HTML document inside the current web page. *(Webpage ke andar dusra webpage load karne wala frame box).*

### Key Concepts
* Elements inside an iFrame cannot be located using normal page locators directly. You must target the frame first.
* Target frames using `page.frameLocator('iframe-selector')`.
* Once targeted, chain standard element locators to interact with elements inside the frame.

### Visual Explanation
```text
  ┌────────────────────────────────────────────────────────┐
  │ Main VJTI ERP Page                                     │
  │   - Label: Student Name                                │
  │   ┌──────────────────────────────────────────────────┐ │
  │   │ iFrame (Bank payment-gateway wrapper)             │ │
  │   │   - Input: Credit Card Number                    │ │
  │   └──────────────────────────────────────────────────┘ │
  └────────────────────────────────────────────────────────┘
```
To fill the credit card number, you must enter the iFrame scope first.

### Syntax
```javascript
const myFrame = page.frameLocator('iframe#payment-frame');
await myFrame.locator('#card-number').fill('1234-5678-9012');
```

### Example
#### Code
```javascript
// Target nested card form inside payment gateway iframe
const paymentFrame = page.frameLocator('iframe[name="paytm-checkout-iframe"]');
await paymentFrame.getByPlaceholder('Card Number').fill('4111 2222 3333 4444');
```

### Common Mistakes
* **Locating elements inside iframes using page.locator():** If you search for an element inside an iframe using `page.locator('#element')`, Playwright will throw an ElementNotFound timeout error. Always use `frameLocator()`.

### Topic Summary
Use `frameLocator('selector')` to access elements inside iframes before executing actions.

---

## JavaScript Alerts

### Definition
**JavaScript Alerts** are native browser dialog windows (Alert, Confirm, Prompt) that display notifications or request inputs, blocking interaction with the main page. *(Browser ke native alert pop-ups jo screen activity block karte hain).*

### Key Concepts
* **Auto-Dismiss:** Playwright automatically dismisses these dialogs by default so your test doesn't hang.
* **Custom Handler:** To accept, check messages, or type inside prompts, register a listener *before* the action triggers the dialog using `page.on('dialog', handler)`.

### Syntax
```javascript
page.on('dialog', async dialog => {
  console.log(dialog.message());
  await dialog.accept(); // Clicks OK
  // Or: await dialog.dismiss(); // Clicks Cancel
});
```

### Example
#### Code
```javascript
// Handle a confirmation dialog on deletion
page.on('dialog', async dialog => {
  expect(dialog.message()).toContain('Are you sure you want to delete?');
  await dialog.accept(); // Click OK
});

await page.getByRole('button', { name: 'Delete Student Record' }).click();
```

### Common Mistakes
* **Registering the listener after the dialog triggers:** If you click the delete button first and then register `page.on('dialog')`, the alert will have already popped up and been auto-dismissed before the handler could capture it.

### Topic Summary
Playwright auto-dismisses alerts by default. Register `page.on('dialog')` before actions to capture messages or accept prompts.

---

## Session Summary

### Key Takeaways
1. **Forms Automation:** Fill, check, and select actions automate basic form components. Direct string fills are preferred for date pickers.
2. **File Handling:** `setInputFiles()` attaches local files directly to input nodes, avoiding OS-level select boxes.
3. **Context Tabs:** `context.waitForEvent('page')` captures dynamically opened browser tabs and popups.
4. **Frame Navigation:** iFrames isolate elements. Access them using `page.frameLocator()` before looking up children.
5. **Dialog Hooks:** JavaScript alerts block standard execution. Register listener events using `page.on('dialog')` before triggering them.

### Important Interview Points
* **How does Playwright handle file uploads?**
  * Playwright uses `setInputFiles()` to set file paths directly on `<input type="file">` elements, avoiding blocking native OS dialogs.
* **How do you handle a new browser tab?**
  * We use `context.waitForEvent('page')` inside a `Promise.all()` block alongside the click action that triggers the tab.
* **Explain how to interact with an element inside an iframe.**
  * Use `page.frameLocator('iframe-selector')` to target the frame, then chain standard locator actions (e.g. `.locator('#btn').click()`).
* **What is Playwright's default behavior for JavaScript alerts?**
  * Playwright automatically dismisses native dialogs (alerts, confirms, prompts) by default so tests do not hang. You can handle them custom by registering `page.on('dialog')` beforehand.

### Quick Revision Sheet

| Control Element | Playwright API | Key Option / State |
| --- | --- | --- |
| **Dropdown** | `selectOption()` | `{ label: 'Visible' }`, `{ index: 1 }` |
| **Checkbox / Radio** | `check()`, `uncheck()` | Idempotent selection, `toBeChecked()` |
| **File Upload** | `setInputFiles()` | Pass filepath string or `[]` to clear |
| **Iframe** | `frameLocator()` | Chain locators: `frameLocator().locator()` |
| **New Tab** | `context.waitForEvent('page')` | Wrap in `Promise.all()` with click |
| **JS Alert** | `page.on('dialog', fn)` | `dialog.accept()`, `dialog.dismiss()` |
