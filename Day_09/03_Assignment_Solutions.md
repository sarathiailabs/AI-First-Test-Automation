# Day 9: Forms + Windows + Frames – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and practical assignments from Day 9.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is the difference between testing standard dropdowns (HTML `<select>`) and custom dropdowns (e.g., built using `<div>` and `<li>` tags)? How do you automate each in Playwright?

### Answer
* **Standard Dropdown (HTML `<select>`):** Built using standard HTML tags. The browser manages options rendering natively.
  * **Automation:** We use the native `selectOption()` API:
    ```javascript
    await page.locator('#branch-select').selectOption('cs');
    ```
* **Custom Dropdown:** Built using custom layouts (React/Angular select controls) where clicking a `<div>` reveals an unordered list `<ul>` of items `<li>`.
  * **Automation:** You cannot use `selectOption()`. You must automate it like a real user:
    1. Click the dropdown toggle card to expand the menu list: `await page.locator('.custom-select-trigger').click();`
    2. Click the specific list option item when it displays: `await page.getByRole('listitem').filter({ hasText: 'Computer Science' }).click();`
* **Hinglish Helper:** *Standard select dropdown ke liye selectOption() use hota hai, custom dropdown (div/li) ke liye click-and-select strategy use hoti hai.*

---

### Question 2 🔥 **Frequently Asked**
How does Playwright's `setInputFiles()` API handle file uploads under the hood? Why is clicking the "Choose File" button discouraged in automated scripts?

### Answer
* **Under the hood:** `setInputFiles()` updates the files property of the target `<input type="file">` node directly in browser memory using developer protocols.
* **Why clicking is discouraged:** Clicking the "Choose File" button opens the operating system's native file explorer dialog. Automation tools run inside the browser sandbox and cannot interact with OS windows. The test will hang forever waiting for the OS window to close. `setInputFiles()` bypasses this dialog entirely.

---

### Question 3 📌 **Important**
How do you handle a scenario where clicking a link opens a new browser tab? Write a code snippet showing how you capture the new page instance and explain the race conditions involved.

### Answer
#### Code Snippet:
```javascript
// Wrap in Promise.all to prevent race conditions
const [newTabPage] = await Promise.all([
  context.waitForEvent('page'), // 1. Set up listener
  page.locator('#pay-fees-btn').click() // 2. Trigger the action
]);
await newTabPage.waitForLoadState('load');
```

#### Race Conditions Explanation:
If you write:
```javascript
await page.locator('#pay-fees-btn').click(); // Tab starts opening
const newTab = await context.waitForEvent('page'); // Listener starts
```
If the application opens the new tab in 5 milliseconds but the driver takes 10 milliseconds to register the listener, the tab event fires before the listener is ready. The test will hang forever waiting for an event that already occurred. Wrapping them in `Promise.all()` sets up the listener *before* the click occurs, preventing this race condition.

---

### Question 4 📌 **Important**
What is the difference between `page.locator()` and `page.frameLocator()`? What error occurs if you try to click a button inside an iFrame using `page.locator()` directly?

### Answer
* **`page.locator()`**: Searches for elements inside the main page HTML document context. It cannot cross iFrame boundaries due to browser security models.
* **`page.frameLocator()`**: Targets the nested HTML document context inside a specific `<iframe>` element first, letting you chain subsequent locators inside the frame scope.
* **Expected Error:** If you try to target an element inside an iframe using `page.locator('#btn').click()`, Playwright will search the main page, fail to find the element, and throw a **TimeoutError: waiting for locator('#btn') to be visible** after 30 seconds.

---

### Question 5 💡 **Good to Know**
Explain how Playwright handles native JavaScript Alerts (`alert`, `confirm`, `prompt`) by default. How do you assert the message of an alert and click "Cancel" instead of "OK"?

### Answer
* **Default Behavior:** Playwright automatically dismisses native browser dialog popups (alerts, confirms, prompts) by default so your test execution doesn't block.
* **To assert messages and select Cancel:** You must register a listener event using `page.on('dialog')` **before** the action that triggers the dialog is executed.
* **Handling code:**
```javascript
// Register listener first
page.on('dialog', async dialog => {
  // Assert message content
  expect(dialog.message()).toBe('Confirm fee transaction?');
  // Click Cancel/Cancel trigger
  await dialog.dismiss();
});

// Trigger action next
await page.locator('#submit-payment').click();
```

---

## Programming Assignment Solutions

### Assignment 1: VJTI Student Registration Form Automation

Below is the complete, executable Playwright test script file `vjti-registration.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Automate VJTI Student Registration Form', async ({ page }) => {
  // 1. Navigate to the registration page
  await page.goto('https://erp.vjti.ac.in/portal/registration');

  // 2. Fill in Full Name input
  await page.getByPlaceholder('Enter Full Name').fill('Rahul Verma');

  // 3. Select branch from dropdown
  await page.locator('#branch-select').selectOption({ label: 'Information Technology' });

  // 4. Select Hostel Required radio button
  const hostelRadio = page.locator('input[name="hostel"][value="yes"]');
  await hostelRadio.check();

  // 5. Fill Date of Birth input field
  await page.locator('#dob').fill('2004-08-15');

  // 6. Check the terms checkbox
  const termsCheckbox = page.locator('#terms');
  await termsCheckbox.check();

  // 7. ASSERTIONS: Verify that both selections are checked
  await expect(hostelRadio).toBeChecked();
  await expect(termsCheckbox).toBeChecked();

  // 8. Click the Register button
  await page.locator('#submit-reg').click();
});
```

#### Explanation
* **`selectOption({ label: ... })`**: Selects the dropdown element matching the visible text option.
* **`check()`**: Selects the checkbox and radio button elements securely.
* **`toBeChecked()`**: Asserts that the toggles are active.

---

### Assignment 2: Profile Picture File Upload Validation

Below is the complete, executable Playwright test script file `vjti-upload.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';
import path from 'path';
import fs from 'fs';

test('Upload profile picture and clear verification', async ({ page }) => {
  await page.goto('https://erp.vjti.ac.in/portal/profile-settings');

  // 1. Create a dummy file in the current directory for testing
  const dummyFilePath = path.join(__dirname, 'mock-profile.png');
  fs.writeFileSync(dummyFilePath, 'dummy image data');

  // 2. Select file upload input element and set files
  const fileInput = page.locator('#photo-upload');
  await fileInput.setInputFiles(dummyFilePath);

  // 3. Assert that the file name is visible on screen
  const nameLabel = page.locator('#uploaded-file-name');
  await expect(nameLabel).toContainText('mock-profile.png');

  // 4. Clear the file by uploading an empty array
  await fileInput.setInputFiles([]);

  // 5. Assert that the label resets
  await expect(nameLabel).toContainText('No file chosen');

  // 6. Clean up: Delete dummy file from local disk
  if (fs.existsSync(dummyFilePath)) {
    fs.unlinkSync(dummyFilePath);
  }
});
```

#### Explanation
* **`setInputFiles(dummyFilePath)`**: Sets the local path directly on the input node, avoiding blocking OS dialog windows.
* **`setInputFiles([])`**: Clears selected files, resetting the element.

---

### Assignment 3: Window, iFrame, & Alert Coordination

Below is the complete, executable Playwright test script file `vjti-payment.spec.ts`.

#### Solution Code
```typescript
import { test, expect } from '@playwright/test';

test('Coordinate payments across tab, iframe, and alert dialogs', async ({ page, context }) => {
  // 1. Navigate to the main fees payment portal
  await page.goto('https://erp.vjti.ac.in/portal/fees');

  // 2. Click Pay Online and capture the new tab page instance
  const [newTab] = await Promise.all([
    context.waitForEvent('page'),
    page.locator('#pay-fees-btn').click()
  ]);

  // 3. Wait for the new tab to load and assert correct URL
  await newTab.waitForLoadState('load');
  await expect(newTab).toHaveURL(/.*checkout/);

  // 4. Locate the iframe container on the new page
  const paymentFrame = newTab.frameLocator('iframe#payment-gateway-frame');

  // 5. Inside the iframe scope, fill the card number input
  await paymentFrame.locator('#card-num').fill('4111 2222 3333 4444');

  // 6. Register a Dialog Listener to intercept the checkout popup confirmation
  newTab.on('dialog', async dialog => {
    // Assert dialog prompt text message
    expect(dialog.message()).toBe('Confirm fee transaction?');
    // Dismiss/Cancel dialog
    await dialog.dismiss();
  });

  // 7. Click Process Payment inside the iframe
  await paymentFrame.locator('#pay-now-trigger').click();
});
```

#### Explanation
* **`waitForEvent('page')`**: Listens for the creation of a new browser tab.
* **`frameLocator('iframe#payment-gateway-frame')`**: Switches context to the payment gateway's document inside the iframe.
* **`newTab.on('dialog')`**: Configures a listener to handle the alert dynamically.
