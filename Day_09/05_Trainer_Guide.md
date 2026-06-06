# Day 9: Forms + Windows + Frames – Trainer Teaching Guide

This guide is designed for instructors delivering the "Forms + Windows + Frames" session. It details the lecture scripts, engagement strategies, whiteboard sketches, live terminal commands, and student coaching tips.

---

## Session Opening

### Welcome Script
"Hello everyone! Welcome to Day 9. Today, we are moving beyond basic clicks and fills. We are going to automate complex forms with dropdown selectors, checkboxes, radio groups, date calendars, and file uploads. More importantly, we will tackle the three biggest challenges in modern UI testing: switching between multiple browser tabs, piercing isolated iFrames, and handling native JavaScript alerts. Let's dive in."

### Session Goal
By the end of today's 2-hour session, you will confidently write Playwright scripts to interact with complex form components, handle binary file attachments, capture dynamic popup windows, inspect elements inside nested iFrames, and configure listeners for JS alert dialogs.

### Motivation
Imagine automating a payment checkout test. The credit card input fields are loaded inside an iframe provided by a payment gateway (like Paytm or Stripe) for security. If your script searches the main page, it throws a timeout error. Then, clicking the pay button opens a secure OTP screen in a new browser tab. How do we automate this flow? Today, you will master these exact multi-tab, multi-frame coordination patterns.

---

## 1. Forms

### Trainer Introduction
"Forms are the primary way web applications gather user inputs. Automating forms in Playwright is straightforward: we locate the container parent form, find individual child elements, fill them, and trigger the submit button click."

### Student Engagement Questions
1. What HTML tag represents a form container? (`<form>`).
2. Why is it a good practice to scope element locators inside the parent form container?
3. How do you trigger form submission without clicking the submit button? (Pressing Enter key on an input field).

### Whiteboard Teaching
Draw form scoping:
```text
  [ Form Wrapper: #registration-form ]
      ├── input[name="username"]  ──► fill()
      ├── select#branch           ──► selectOption()
      └── button#submit-reg       ──► click()
```

### Teaching Flow
1. Introduce the `<form>` HTML element.
2. Discuss locator scoping within form boundaries.
3. Show how to trigger form submissions.

### Live Coding Demonstration
#### Step 1
Write a basic form block:
```typescript
const form = page.locator('#registration-form');
await form.locator('#fullname').fill('Rahul Verma');
```
#### Step 2
Discuss scoping elements within the form to prevent matching duplicate inputs.

### Transition Script
"Forms consist of individual controls. Let's start with the dropdown selector: the Dropdown option API."

---

## 2. Dropdowns

### Trainer Introduction
"Standard dropdowns use the `<select>` tag with nested `<option>` items. In Playwright, we do not click standard options. Instead, we use `selectOption()`, which lets us select options by label, index, or value attribute."

### Student Engagement Questions
1. Can you select multiple options in a single dropdown? (Yes, if it has the `multiple` attribute).
2. What is the difference between a value attribute and a visible label?
3. Why should you avoid raw clicks on option tags?

### Whiteboard Teaching
Draw dropdown structure:
```html
  <select id="branch-select">
    <option value="cs">Computer Science</option>  <── Value is "cs", Label is "Computer Science"
  </select>
```
Show matching Playwright commands:
* By Value: `selectOption('cs')`
* By Label: `selectOption({ label: 'Computer Science' })`

### Teaching Flow
1. Define standard dropdown elements.
2. Introduce the `selectOption()` API.
3. Discuss value vs. label vs. index selections.
4. Contrast standard select boxes with custom div-based dropdown lists.

### Live Coding Demonstration
#### Step 1
Write a dropdown selection snippet:
```typescript
const dropdown = page.locator('#branch-select');
await dropdown.selectOption('cs'); // By value
await dropdown.selectOption({ label: 'Information Technology' }); // By label
```
#### Step 2
Explain that Playwright performs actionability checks on the select box before executing the selection.

### Transition Script
"Next, let's look at checkboxes, which allow binary selections on forms."

---

## 3. Checkboxes

### Trainer Introduction
"To toggle options on or off, we use checkboxes. In Playwright, we use `check()` and `uncheck()`. These APIs are idempotent: calling `.check()` on a checkbox that is already checked will not uncheck it, whereas a raw `.click()` would toggle it off."

### Student Engagement Questions
1. What is the definition of 'idempotent'?
2. What assertion do you use to verify if a checkbox is checked? (`toBeChecked()`).
3. Why is `check()` safer than `click()` for checkbox inputs?

### Whiteboard Teaching
Compare click vs check:
```text
  Checkbox State: [ Checked (True) ]
  - locator.click()   ──► toggles state to [ Unchecked (False) ] (Accidental change!)
  - locator.check()   ──► maintains state at [ Checked (True) ]   (Safe!)
```

### Teaching Flow
1. Define checkbox elements.
2. Introduce the `check()` and `uncheck()` APIs.
3. Explain the safety benefits of check over click.
4. Write checkbox assertions.

### Live Coding Demonstration
#### Step 1
Write a checkbox interaction snippet:
```typescript
const termsCheck = page.locator('#terms');
await termsCheck.check();
await expect(termsCheck).toBeChecked();
```
#### Step 2
Demonstrate using `uncheck()` to deselect options and verify the state.

### Transition Script
"Similar to checkboxes, radio buttons allow selections, but restrict the user to a single option within a group."

---

## 4. Radio Buttons

### Trainer Introduction
"Radio buttons let users select exactly one option from a group. Selecting a new radio button in the same group automatically deselects the previous selection. We use the same `check()` API to interact with them."

### Student Engagement Questions
1. What HTML attribute groups radio buttons together? (The `name` attribute).
2. What happens if you call `uncheck()` on a radio button? (Playwright will throw an error; you cannot uncheck a radio button directly, you must select a different radio option in the group).

### Whiteboard Teaching
Draw radio grouping:
```html
  <input type="radio" name="hostel" value="yes" /> Yes
  <input type="radio" name="hostel" value="no" /> No
```
Show that selecting "yes" de-selects "no" automatically.

### Teaching Flow
1. Define grouped radio buttons.
2. Introduce `check()` for radio buttons.
3. Assert checked states.

### Live Coding Demonstration
#### Step 1
Write a radio button test block:
```typescript
const yesRadio = page.locator('input[name="hostel"][value="yes"]');
await yesRadio.check();
await expect(yesRadio).toBeChecked();
```
#### Step 2
Point out that we do not manually uncheck the "no" option; checking "yes" handles this automatically.

### Transition Script
"Now, let's look at date pickers, which are used to enter date formats in forms."

---

## 5. Date Pickers

### Trainer Introduction
"Date pickers let users select dates, months, and years. In test automation, if the input field allows text entry, the most stable and fastest approach is to fill the date string directly (usually in 'YYYY-MM-DD' format) using `fill()`. If the input is read-only, you must automate clicking the calendar widget elements."

### Student Engagement Questions
1. Why is filling dates directly preferred over clicking calendar dates manually?
2. What format do standard HTML date inputs expect? (YYYY-MM-DD).
3. How do you handle read-only input elements? (Click the calendar trigger or clear the read-only attribute using JavaScript).

### Whiteboard Teaching
Show calendar vs. text fill:
```text
  Calendar UI clicks: (5-10 clicks)
  [ Click input ] ──► [ Next Month ] ──► [ Next Month ] ──► [ Click Day 15 ] (Slow & Fragile)
  
  Direct Fill: (1 action)
  await locator.fill('2004-08-15') (Fast & Stable)
```

### Teaching Flow
1. Explain input date controls.
2. Detail direct text entry vs. calendar widget automation.
3. Show how to write date entries.

### Live Coding Demonstration
#### Step 1
Write a date fill snippet:
```typescript
await page.locator('#dob').fill('2004-08-15');
```
#### Step 2
Discuss how to automate calendar clicks if the field is read-only.

### Transition Script
"Next, let's look at uploading files on forms."

---

## 6. File Upload

### Trainer Introduction
"To automate file uploads, Playwright provides the `setInputFiles()` API. It is critical to note that we do **not** click the file upload button, as this opens native OS file selection dialogs that automation tools cannot control. Instead, we use `setInputFiles()` to set file paths directly on the input node."

### Student Engagement Questions
1. Why can't automation tools like Playwright interact with OS file explorer windows? (OS windows run outside the browser sandbox).
2. How do you clear a file upload? (Pass an empty array `[]`).
3. How do you upload multiple files simultaneously? (Pass an array of filepaths).

### Whiteboard Teaching
Draw file input injection:
```text
  OS Dialog Block (Avoid):
  [ Click "Browse" ] ──► [ OS Window Opens ] ──► [ Test Hangs! ]
  
  Playwright Direct Injection (Recommended):
  [ locator.setInputFiles('path') ] ──► Injects file metadata directly into DOM node
```

### Teaching Flow
1. Explain the file input element (`<input type="file">`).
2. Introduce the `setInputFiles()` API.
3. Discuss path resolution using Node's `path` library.
4. Show how to clear uploads.

### Live Coding Demonstration
#### Step 1
Write a file upload test block:
```typescript
import path from 'path';

test('File Upload Demo', async ({ page }) => {
  await page.goto('/profile');
  const filePath = path.join(__dirname, 'mock-profile.png');
  
  // Direct file attachment
  await page.locator('#photo-upload').setInputFiles(filePath);
});
```
#### Step 2
Demonstrate clearing the upload:
```typescript
await page.locator('#photo-upload').setInputFiles([]);
```

### Transition Script
"Now, let's tackle popups and tabs, which open when clicking links with target='_blank'."

---

## 7. Popups & Tabs

### Trainer Introduction
"When clicking a link opens a new tab, it runs inside the same browser context. To capture this new page instance, we use the `waitForEvent('page')` API. We must set up the event listener *before* we perform the action that triggers the tab to prevent race conditions."

### Student Engagement Questions
1. Do new tabs share session cookies with the parent tab? (Yes, if they run in the same Browser Context).
2. What happens if you click a link first and then write `waitForEvent('page')`? (A race condition can occur if the tab loads faster than the listener registers, causing the test to hang).

### Whiteboard Teaching
Draw the tab capture promise structure:
```text
                     Promise.all
      ┌───────────────────┴───────────────────┐
      ▼                                       ▼
  1. Set up listener:                     2. Click action:
  context.waitForEvent('page')            page.click('#link')
      │                                       │
      └───────────────────┬───────────────────┘
                          ▼
              [ Captures newTab page ]
```

### Teaching Flow
1. Explain how tabs run in the same browser context.
2. Outline the race conditions involved in page events.
3. Introduce the `Promise.all()` wrapper pattern.

### Live Coding Demonstration
#### Step 1
Write a tab capture snippet:
```typescript
const [newTab] = await Promise.all([
  context.waitForEvent('page'),
  page.locator('#pay-fees-btn').click()
]);
await newTab.waitForLoadState('load');
await expect(newTab).toHaveURL(/.*checkout/);
```
#### Step 2
Explain why this sequence is safe and how to run subsequent actions on the `newTab` page object reference.

### Transition Script
"If you have multiple tabs open, you can retrieve them all and switch control using Multiple Windows APIs."

---

## 8. Multiple Windows

### Trainer Introduction
"To manage and switch focus between multiple open tabs, we use `context.pages()`. This returns an array of active page references, allowing you to switch control simply by calling actions on the target page reference."

### Student Engagement Questions
1. How do you get the total count of open tabs? (`context.pages().length`).
2. How do you close a specific tab? (`await targetTab.close()`).

### Teaching Flow
1. Explain `context.pages()`.
2. Show how to index page objects from the array.
3. Detail how to close unused tabs.

### Live Coding Demonstration
#### Step 1
Write a tab indexing script:
```typescript
const tabs = context.pages();
const secondaryTab = tabs[1];
await secondaryTab.bringToFront();
await secondaryTab.close();
```
#### Step 2
Show the browser tabs closing dynamically.

### Transition Script
"Another challenge in web automation is dealing with nested documents: iFrames. Let's see how Playwright accesses them."

---

## 9. Frames & iFrames

### Trainer Introduction
"An iFrame embeds an independent HTML page inside the current document. You cannot locate elements inside an iframe using normal page locators directly. You must use `page.frameLocator('iframe-selector')` to target the frame first, then chain selectors inside it."

### Student Engagement Questions
1. Why does `page.locator()` fail to find elements inside an iframe? (Due to security boundaries and independent page scopes).
2. What is the difference between `locator()` and `frameLocator()`?
3. What is a common scenario where iframes are used? (Payment gateways, Google Maps integrations, YouTube embeds).

### Whiteboard Teaching
Draw iframe boundaries:
```text
  [ Main Page DOM ]
    ├── locator('#btn') ──► (Finds main buttons)
    └── [ iframe#payment-frame ] ──► frameLocator('iframe#payment-frame')
             └── locator('#card-num') ──► (Finds input inside frame)
```

### Teaching Flow
1. Define the iFrame element.
2. Explain why standard locators fail.
3. Introduce the `frameLocator()` API.
4. Show how to chain selectors inside the frame.

### Live Coding Demonstration
#### Step 1
Write an iframe locator snippet:
```typescript
const payFrame = page.frameLocator('iframe#payment-gateway-frame');
await payFrame.locator('#card-num').fill('4111 2222 3333 4444');
```
#### Step 2
Explain how Playwright switches context to the iframe document and performs actionability checks on the nested element.

### Transition Script
"Finally, let's look at how to handle native browser dialogs: JavaScript Alerts."

---

## 10. JavaScript Alerts

### Trainer Introduction
"Native alerts, confirmation boxes, and prompts are triggered by the browser and block interaction with the page. Playwright automatically dismisses these dialogs by default so tests do not hang. To handle them custom (e.g., asserting messages or clicking OK/Cancel), you must register a `dialog` listener before triggering them."

### Student Engagement Questions
1. What does Playwright do to browser dialogs by default? (Dismisses them automatically).
2. What are the three types of native JavaScript dialogs? (Alert, Confirm, Prompt).
3. Why must the dialog listener be registered before the action that triggers the dialog? (To prevent race conditions).

### Whiteboard Teaching
Draw dialog event cycle:
```text
  1. Register Listener:   page.on('dialog', async dialog => { ... })
  2. Click Button:        await page.click('#delete-btn')
  3. Dialog Appears:      Listener catches event ──► dialog.accept() (Click OK)
```

### Teaching Flow
1. Explain native browser dialogs.
2. Explain Playwright's auto-dismiss behavior.
3. Introduce the `page.on('dialog')` event handler.
4. Detail accept vs. dismiss options.

### Live Coding Demonstration
#### Step 1
Write a dialog handler test block:
```typescript
page.on('dialog', async dialog => {
  expect(dialog.message()).toBe('Confirm fee transaction?');
  await dialog.dismiss(); // Click Cancel
});
await page.locator('#pay-now-trigger').click();
```
#### Step 2
Run the test and show that the dialog is captured, verified, and dismissed.

---

## Session Closing

### Session Summary
"Today we covered:
1. Forms and core inputs (dropdowns, checkboxes, radio buttons, date pickers).
2. File uploads using `setInputFiles()`.
3. Navigating and capturing new tabs using `context.waitForEvent('page')`.
4. Locating elements inside nested iFrames using `frameLocator()`.
5. Intercepting native JavaScript alerts using `page.on('dialog')`."

### Knowledge Check Questions
1. How do you clear a file upload input? (Pass an empty array `[]`).
2. Why is `check()` preferred over `click()` for checkboxes? (It is idempotent).
3. How do you switch context to an iframe? (Use `page.frameLocator()`).
4. What happens to browser dialogs by default in Playwright? (They are auto-dismissed).
5. Where should you register a `dialog` event listener? (Before the action that triggers the dialog).

### Assignment Introduction
"To practice these concepts, open `02_Assignments.md`. You will automate a student registration form, upload profile photos, and coordinate payments across tabs, iframes, and alert dialogs. These tasks mirror real-world industry challenges."

### Homework Guidance
* Complete the three assignments.
* Try running your script in headed mode and watch the tabs open and close.
* Inspect a payment page that uses iframes and inspect its element structure.

### Next Session Preview
"In our next session, we will look at Advanced UI Automation. We will learn how to handle dynamic elements, tables, pagination, auto-suggestions, infinite scrolls, and downloads."
