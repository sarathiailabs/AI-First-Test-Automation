# Day 9: Forms + Windows + Frames – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of form inputs (dropdowns, checkboxes, radios, calendars), file uploads, multi-tab window contexts, frame locators, and JavaScript alert listeners.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the difference between testing standard dropdowns (HTML `<select>`) and custom dropdowns (e.g., built using `<div>` and `<li>` tags)? How do you automate each in Playwright?

### Question 2 🔥 **Frequently Asked**
How does Playwright's `setInputFiles()` API handle file uploads under the hood? Why is clicking the "Choose File" button discouraged in automated scripts?

### Question 3 📌 **Important**
How do you handle a scenario where clicking a link opens a new browser tab? Write a code snippet showing how you capture the new page instance and explain the race conditions involved.

### Question 4 📌 **Important**
What is the difference between `page.locator()` and `page.frameLocator()`? What error occurs if you try to click a button inside an iFrame using `page.locator()` directly?

### Question 5 💡 **Good to Know**
Explain how Playwright handles native JavaScript Alerts (`alert`, `confirm`, `prompt`) by default. How do you assert the message of an alert and click "Cancel" instead of "OK"?

---

## Practical Assignments

### Assignment 1: VJTI Student Registration Form Automation

* **Interview Relevance:** Assesses your capability to automate a complete business registration form containing multiple input fields, select boxes, checkboxes, radio buttons, and calendars.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `selectOption`, `check`, `uncheck`, `fill`, `isChecked` assertions.

#### Problem Statement
Write a Playwright test named `vjti-registration.spec.ts` that automates filling out the VJTI Student Registration Form.

**HTML Layout of Registration Form:**
```html
<form id="registration-form">
  <!-- Text Input -->
  <input type="text" id="fullname" placeholder="Enter Full Name" />

  <!-- Dropdown Select -->
  <label for="branch-select">Select Branch</label>
  <select id="branch-select">
    <option value="">-- Choose Branch --</option>
    <option value="cs">Computer Science</option>
    <option value="it">Information Technology</option>
    <option value="extc">Electronics & Telecomm</option>
  </select>

  <!-- Radio Group -->
  <div class="radio-group">
    <label><input type="radio" name="hostel" value="yes" /> Hostel Required</label>
    <label><input type="radio" name="hostel" value="no" /> Day Scholar</label>
  </div>

  <!-- Date Picker -->
  <label for="dob">Date of Birth</label>
  <input type="date" id="dob" />

  <!-- Checkbox -->
  <label>
    <input type="checkbox" id="terms" /> I agree to the terms
  </label>

  <button type="submit" id="submit-reg">Register</button>
</form>
```

#### Requirements
1. Navigate to the registration portal.
2. Fill the Full Name field with `"Rahul Verma"`.
3. Select `"Information Technology"` from the branch select dropdown.
4. Select the `"Hostel Required"` radio button.
5. Enter `"2004-08-15"` in the Date of Birth calendar field.
6. Check the terms and conditions checkbox.
7. Assert that the terms checkbox and the hostel radio button are checked.
8. Click the `"Register"` button.

#### Hints
* Select option by visible text using `{ label: 'Information Technology' }`.
* Date pickers on standard HTML forms accept dates formatted as `'YYYY-MM-DD'`.

---

### Assignment 2: Profile Picture File Upload Validation

* **Interview Relevance:** File uploads are a standard profile setting feature. This validates file API handling and error state assertions.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `setInputFiles`, file path configurations, warning validations.

#### Problem Statement
Write a Playwright test named `vjti-upload.spec.ts` that automates uploading a student profile picture on the student dashboard. You must upload a valid image file, verify the upload name is shown, and then clear the file to verify the reset state.

**HTML Layout:**
```html
<div class="upload-panel">
  <h2>Upload Profile Photo</h2>
  <input type="file" id="photo-upload" />
  <span id="uploaded-file-name">No file chosen</span>
  <button id="clear-upload">Remove Photo</button>
</div>
```

#### Requirements
1. Navigate to the profile settings portal.
2. Prepare a mock image file named `mock-profile.png` inside your project workspace.
3. Locate the file input field.
4. Upload `mock-profile.png` using `setInputFiles()`.
5. Assert that the text of `#uploaded-file-name` contains `"mock-profile.png"`.
6. Click the `"Remove Photo"` button (or clear files programmatically by passing `[]`).
7. Assert that the text resets to `"No file chosen"`.

#### Hints
* Use `path.join(__dirname, 'mock-profile.png')` to resolve file paths dynamically.
* Clear files by writing: `await page.locator('#photo-upload').setInputFiles([])`.

---

### Assignment 3: Window, iFrame, & Alert Coordination

* **Interview Relevance:** An advanced coordination challenge commonly asked in senior QA rounds. Tests synchronization of multiple windows, frame locators, and alert listeners inside a single workflow.
* **Difficulty Level:** Advanced
* **Concepts Covered:** Multi-tabs, `frameLocator`, `page.on('dialog')`, `Promise.all`.

#### Problem Statement
Write a Playwright test named `vjti-payment.spec.ts` that automates the VJTI semester fee check out. The test must:
1. Click a button that opens a checkout page in a new browser tab.
2. Switch context to the new payment tab.
3. Fill credit card details nested inside a checkout iFrame.
4. Trigger the pay button, which displays a native browser confirmation alert.
5. Verify the alert message and click "Cancel".

**HTML Layout of Main Page:**
```html
<button id="pay-fees-btn">Pay Online (New Tab)</button>
```

**HTML Layout of New Tab Page (`/checkout`):**
```html
<div class="payment-portal">
  <iframe id="payment-gateway-frame" src="payment-details.html">
    <!-- Inside Frame -->
    <html>
      <body>
        <input type="text" id="card-num" placeholder="Card Number" />
        <button id="pay-now-trigger">Process Payment</button>
      </body>
    </html>
  </iframe>
</div>
```

#### Requirements
1. Navigate to the main fees page.
2. Setup a tab capture promise block, click `#pay-fees-btn`, and capture the new tab page instance.
3. Wait for the new tab to load and assert it has the correct checkout url.
4. Inside the new page, locate the iFrame `iframe#payment-gateway-frame`.
5. Inside that iFrame scope, fill the card number input with `"4111 2222 3333 4444"`.
6. Register a Dialog Listener on the new page to listen for the confirmation popup.
7. Click the `#pay-now-trigger` button inside the iFrame.
8. Assert that the alert text displays `"Confirm fee transaction?"` and programmatically click **Cancel/Dismiss**.

#### Hints
* Wrap the tab capture in:
  ```javascript
  const [newTab] = await Promise.all([
    context.waitForEvent('page'),
    page.locator('#pay-fees-btn').click()
  ]);
  ```
* Handle dialogs using `dialog.dismiss()` to click Cancel.
