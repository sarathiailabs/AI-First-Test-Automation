# Day 10: Advanced UI Automation – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of dynamic tables, pagination steps, dynamic dropdown autocomplete recommendations, infinite scrolls, file downloads, and configuration retry limits.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
Write a code snippet showing how you locate a cell value inside a dynamic table where rows change order on every page refresh. Describe your selector strategy.

### Question 2 🔥 **Frequently Asked**
How do you handle autocomplete search boxes (auto-suggestions) in Playwright? Why can using `locator.fill()` sometimes fail to trigger suggestions?

### Question 3 📌 **Important**
How do you automate and validate file downloads in Playwright? Write a code snippet showing how to capture the download event and save the file to a custom project folder.

### Question 4 📌 **Important**
Explain **Lazy Loading** and **Infinite Scroll**. How do you write automation scripts to verify that lazy-loaded elements render correctly when scrolled?

### Question 5 💡 **Good to Know**
What is the difference between **Test Retries** and **Action Retries**? How does configuring retries help manage flaky tests in CI pipelines, and what are the limitations?

---

## Practical Assignments

### Assignment 1: VJTI Student Registry Dynamic Table & Pagination

* **Interview Relevance:** Grid and table validation is a standard requirement for enterprise portal testing. Verifies capability to search, filter rows, click pagination triggers, and sync state updates.
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** Table row filtering, indexing cell columns, pagination click, element detachment checks.

#### Problem Statement
Write a Playwright test named `vjti-table.spec.ts` that automates searching the VJTI Student Registry table. Search for `"Sharma"`, extract the roll number from the matching row, click the "Next" page button to navigate, and assert that new rows load.

**Registry HTML Markup (Page 1):**
```html
<div class="registry-section">
  <input type="text" id="search-student" placeholder="Search by name" />
  
  <table id="student-grid">
    <thead>
      <tr>
        <th>Roll No</th>
        <th>Student Name</th>
        <th>Branch</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>1002</td>
        <td>Amit Sharma</td>
        <td>Computer Science</td>
      </tr>
      <tr>
        <td>1005</td>
        <td>Rahul Verma</td>
        <td>Information Technology</td>
      </tr>
    </tbody>
  </table>

  <div class="pagination-controls">
    <button class="page-link" id="prev-btn">Previous</button>
    <button class="page-link" id="next-btn">Next</button>
  </div>
</div>
```

#### Requirements
1. Navigate to the student registry page.
2. Fill the search input with `"Sharma"`.
3. Locate the row containing `"Amit Sharma"` dynamically.
4. Extract the Roll Number value (`"1002"`) from the first column cell of that row.
5. Assert that the Roll Number is correct.
6. Clear the search input, then click the `"Next"` page button.
7. Wait for the old page rows to be detached from the DOM to ensure the page reloaded.
8. Assert that the new page contains page-2 student records.

#### Hints
* Filter rows using `page.locator('#student-grid tbody tr').filter({ hasText: 'Amit Sharma' })`.
* Wait for page reloads by waiting for elements from the first page to detach: `await oldRow.waitFor({ state: 'detached' })`.

---

### Assignment 2: Syllabus PDF Download & Validation

* **Interview Relevance:** File download handling is a common senior QA round question. This tests event-based assertions and integration with Node's file system (`fs`).
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `waitForEvent('download')`, saving files, filesystem verification.

#### Problem Statement
Write a Playwright test named `vjti-download.spec.ts` that automates downloading the VJTI Computer Engineering Syllabus PDF from the course page. Save the downloaded file locally in your project folder and verify it was downloaded successfully.

**HTML Markup:**
```html
<div class="syllabus-panel">
  <h2>Syllabus Downloads</h2>
  <a href="/downloads/CS_Syllabus_2026.pdf" id="download-cs-syllabus-btn">Download PDF</a>
</div>
```

#### Requirements
1. Navigate to the syllabus portal page.
2. Setup a download capture event listener.
3. Click the `#download-cs-syllabus-btn` link to trigger the download.
4. Capture the download payload object.
5. Save the file inside a local folder in your project named `downloads/syllabus.pdf`.
6. Assert that the file exists in the directory.
7. Clean up by deleting the downloaded file after verification.

#### Hints
* Setup the event listener using:
  ```javascript
  const [download] = await Promise.all([
    page.waitForEvent('download'),
    page.locator('#download-cs-syllabus-btn').click()
  ]);
  ```
* Import Node's `fs` library to check file existence: `fs.existsSync(filePath)`.

---

### Assignment 3: Multi-Step Scholarship Application Form

* **Interview Relevance:** Wizards and multi-step inputs verify capability to maintain state transitions across multiple screens and assert UI indicators (like progress steps).
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** Wizard forms filling, step transitions, progress indicators, final submission assertions.

#### Problem Statement
Write a Playwright test named `vjti-scholarship.spec.ts` that automates a 3-step VJTI Scholarship application wizard. The test must navigate through all steps, select inputs, and submit the final confirmation.

**Application Wizard HTML Layout:**
```html
<div class="scholarship-wizard">
  <div class="wizard-header">
    <span class="step-indicator">Step 1 of 3</span>
  </div>

  <!-- Step 1 Pane -->
  <div class="step-pane" id="step-1-pane">
    <input type="text" id="parent-income" placeholder="Annual Income" />
    <button type="button" class="next-step-btn">Next</button>
  </div>

  <!-- Step 2 Pane (Hidden initially) -->
  <div class="step-pane" id="step-2-pane" style="display: none;">
    <select id="marks-gpa">
      <option value="9">9.0+ GPA</option>
      <option value="8">8.0+ GPA</option>
    </select>
    <button type="button" class="next-step-btn">Next</button>
  </div>

  <!-- Step 3 Pane (Hidden initially) -->
  <div class="step-pane" id="step-3-pane" style="display: none;">
    <p>Please review details and submit.</p>
    <button type="submit" id="submit-application-btn">Submit Application</button>
  </div>
</div>
```

#### Requirements
1. Navigate to the scholarship portal page.
2. Assert that the step indicator displays `"Step 1 of 3"`.
3. Fill the Annual Income input with `"450000"`.
4. Click the `"Next"` button.
5. Assert that the step indicator updates to `"Step 2 of 3"`.
6. Select `"9.0+ GPA"` from the GPA select dropdown.
7. Click the `"Next"` button.
8. Assert that the step indicator updates to `"Step 3 of 3"`.
9. Click the `"Submit Application"` button.
10. Assert that a success message is displayed on screen.

#### Hints
* Form containers often hide/show using styles (display: none). Playwright's auto-wait will automatically check element actionability when panels transition.
