# Day 5: Software Testing Fundamentals – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and practical assignments provided for Day 5.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is the primary difference between the Software Development Life Cycle (SDLC) and the Software Testing Life Cycle (STLC)? How do they interact during an Agile sprint, and why is "Shift Left" testing recommended?

### Answer
* **SDLC (Software Development Life Cycle):** Focuses on the overall software creation process, including planning, architectural design, database creation, coding, deployment, and maintenance. It is a product development framework.
* **STLC (Software Testing Life Cycle):** Runs parallel to SDLC but focuses strictly on quality validation. It includes analyzing requirements, writing test cases, preparing test environments, executing tests, logging defects, and summarizing test outcomes.
* **Agile Sprint Interaction:** In an Agile sprint, they run concurrently. When developers are planning user stories (SDLC Requirement/Design), QAs analyze requirements and write test scenarios (STLC). As developers code (SDLC Implementation), QAs prepare environments and write automation scripts. As soon as code is committed, QA executes test runs (STLC Execution) and triages bugs.
* **Shift Left Testing:** "Shift Left" means testing as early as possible in the lifecycle. By analyzing requirements and writing tests during the design phase, QA can catch bugs before coding begins, preventing expensive rebuild costs.

---

### Question 2 🔥 **Frequently Asked**
Describe the typical phases of the Defect/Bug Lifecycle. What are the key criteria that must be met before QA can change a bug status from "Fixed" to "Closed"? Under what conditions would a bug status be set to "Deferred" or "Rejected"?

### Answer
* **Defect Lifecycle Phases:**
  1. **New:** Bug is logged in Jira/GitHub.
  2. **Assigned:** Bug is routed to a specific developer for debugging.
  3. **Open:** Developer is actively fixing the bug.
  4. **Fixed:** Developer commits code and deploys a hotfix build.
  5. **Retest:** QA tests the bug fix.
  6. **Verified:** QA confirms the bug is fixed.
  7. **Closed:** QA marks the bug ticket as resolved.
* **Criteria for "Closed" state:**
  1. The bug must be verified in the same environment where it was originally reported, or a designated QA environment.
  2. The fix must be verified using the original steps to reproduce, plus additional regression checks to ensure other components did not break.
  3. The verification notes (including logs, screenshots, or screen recordings) must be documented in the ticket.
* **Deferred vs. Rejected:**
  * **Deferred:** The bug is valid, but fixing it is postponed to a future sprint/release cycle (e.g., legacy browser crash).
  * **Rejected:** The bug is not valid (e.g., incorrect test steps, expected behavior, or unable to reproduce).

---

### Question 3 📌 **Important**
Explain the concepts of **Severity** and **Priority** in defect tracking. Provide a brief example of a bug that is classified as **Low Severity but High Priority**, and another that is **High Severity but Low Priority**.

### Answer
* **Severity:** Represents the technical impact of a defect on the system (e.g., does it cause crashes, data corruption, or memory leaks?). It is determined by QA.
* **Priority:** Represents the business urgency of resolving the bug (e.g., does it affect marketing campaigns, block sales, or impact key clients?). It is determined by Product Owners.
* **Low Severity, High Priority Example:**
  The college logo on the student portal header has a spelling mistake: "VJTI Mumabi" instead of "VJTI Mumbai".
  * *Severity is Low* because it does not block any app features (students can still log in and pay fees).
  * *Priority is High* because it damages college reputation and is highly visible to the public.
* **High Severity, Low Priority Example:**
  The portal crashes to a black screen when a student attempts to update their profile using Internet Explorer 11.
  * *Severity is High* because the app crashes completely.
  * *Priority is Low* because Internet Explorer 11 is no longer officially supported, representing less than 0.1% of active students.

---

### Question 4 📌 **Important**
How do Smoke Testing and Sanity Testing differ? In a CI/CD automation pipeline, which of the two testing types is typically run first and why?

### Answer
* **Differences:**
  * **Smoke Testing:** Broad verification checking if the build is stable enough for deeper testing. It covers the core flows (e.g., login, fee payment homepage) and is run on initial builds.
  * **Sanity Testing:** Narrow, focused verification checking if a specific bug fix or minor update works. It does not cover the entire app, only the modified area.
* **Pipeline Execution:** **Smoke testing** is run first. When a developer pushes code, a smoke test runs immediately to ensure the server starts and login works. If this passes, regression or sanity suites are executed.

---

### Question 5 💡 **Good to Know**
What is the purpose of **Acceptance Criteria** in a user story? Contrast this with the purpose of a **Test Case**. Can a user story have multiple test cases for a single acceptance criterion?

### Answer
* **Acceptance Criteria (AC):** Written by Product Owners to define the high-level boundary conditions and business rules a feature must satisfy to be considered complete (e.g., "Passwords must be at least 8 characters").
* **Test Case:** Written by QAs to translate the AC into step-by-step validation instructions.
* **Multiple Test Cases per AC:** Yes. A single Acceptance Criterion often maps to multiple positive and negative test cases (e.g., testing exactly 7, 8, and 9 characters to check boundary rules, testing symbols, and testing empty inputs).

---

## Practical Assignment Solutions

### Assignment 1: Explain SDLC (Phase Mapping)

#### Solutions
1. **Requirement Analysis:** Deciding what payment gateways (like Paytm UPI) to build with stakeholders belongs here. It shapes the initial product backlog.
2. **Design:** Designing database schemas and schemas to store transaction tokens translates business rules into developer architecture blueprints.
3. **Testing:** Running automated scripts on a staging environment validates software quality and belongs to the testing phase.
4. **Implementation (Coding):** Writing backend Node.js API logic is coding, where developers build the actual software features.
5. **Deployment:** Pushing code builds to AWS production servers makes the software available to students.

---

### Assignment 2: Explain STLC (Process Deliverables)

#### Solutions
| STLC Phase | Input (Entry Criteria) | Key QA Deliverable | Output (Exit Criteria) |
| --- | --- | --- | --- |
| **Requirement Analysis** | Requirements document (BRD/User Stories) | RTM (Requirement Traceability Matrix) | All requirements analyzed and clarified |
| **Test Planning** | Final Requirements & Project Timeline | Test Plan Document (Strategy & Estimates) | Test Plan approved by managers |
| **Test Case Dev** | Approved Test Plan & Specs | Test Cases (with steps, test data) | Test cases written and reviewed |
| **Env Setup** | Architecture details & Test Data | Test Environment Ready | Test environment verified as stable |
| **Test Execution** | Stable Build & Verified Test Environment | Defect Log (Bugs) / Execution Report | All planned test cases run |
| **Test Cycle Closure** | Completed Execution Reports | Test Summary Report | QA sign-off complete |

---

### Assignment 3: Explain Bug Lifecycle (Defect Report)

#### Solution Bug Report
* **Defect ID:** BUG-FEES-01
* **Title:** App crash to raw database stack-trace screen when submitting fee payment with empty roll number field.
* **Severity:** Major (Application crashes to system console log)
* **Priority:** High (Blocks standard student checkout/payment flows)
* **Preconditions:** User is logged in and has a pending fee invoice.
* **Steps to Reproduce:**
  1. Navigate to the `/checkout` page.
  2. Leave the "Student Roll Number" input field blank.
  3. Enter a valid Paytm UPI ID `rahul@paytm` in the payment form.
  4. Click the "Submit Payment" button.
* **Actual Result:** The browser redirects to a raw Node.js database stack-trace screen showing SQL syntax errors.
* **Expected Result:** The application should block submission, remain on the checkout page, and display a validation warning: `"Student Roll Number is required."`

#### Lifecycle Status Trace
```text
[New] ──► [Assigned] ──► [Open] ──► [Fixed] ──► [Retest] ──► [Verified] ──► [Closed]
```
1. **New:** QA discovers and logs BUG-FEES-01 in Jira.
2. **Assigned:** Triage team assigns the ticket to a backend developer.
3. **Open:** Developer investigates the connection error.
4. **Fixed:** Developer wraps database operations in a try-catch block and pushes the hotfix.
5. **Retest:** QA deploys the update to the test environment and tests payments with blank roll numbers.
6. **Verified:** QA verifies validation errors appear properly.
7. **Closed:** QA closes the ticket as resolved.

---

### Assignment 4: Severity vs. Priority Classification

#### Solutions
1. **Scenario A (Dashboard Logo Typo):**
   * **Severity:** Minor (The app functions correctly; students can pay fees).
   * **Priority:** High (Homepage typo damages brand reputation and is highly visible).
2. **Scenario B (Paytm UPI Gateway 500 Error):**
   * **Severity:** Blocker (Payment core path is completely broken).
   * **Priority:** High (Directly stops transactions, causing immediate revenue loss).
3. **Scenario C (Legacy Browser Password Crash):**
   * **Severity:** Major (The app crashes to a black screen).
   * **Priority:** Low (The target browser is unsupported and represents less than 0.1% of active users).
4. **Scenario D (Rules & Regulations Footer Blank Link):**
   * **Severity:** Minor (Only a secondary legal link is blank; primary portal paths are unaffected).
   * **Priority:** Low (A low-traffic page that can be fixed in a future release cycle).

---

### Assignment 5: Create 5 Test Cases (Student Fees Checkout)

#### Solutions

#### TC 1: Valid Checkout Flow (Happy Path)
* **Test Case ID:** TC-FEES-01
* **Title:** Verify successful fee payment using a valid roll number and UPI ID.
* **Preconditions:** Student has a pending invoice of ₹45000 in their database record.
* **Execution Steps:**
  1. Navigate to the `/checkout` page.
  2. Enter Roll Number `"1005"`.
  3. Enter Paytm UPI ID `"rahul@paytm"`.
  4. Click "Submit Payment".
* **Expected Result:** Payment completes successfully, user redirects to `/success`, and downloadable receipt options appear.

---

#### TC 2: Scholarship Code Application
* **Test Case ID:** TC-FEES-02
* **Title:** Verify 10% discount is applied when using code `STUDENT10`.
* **Preconditions:** Student has a pending invoice of ₹45000.
* **Execution Steps:**
  1. Navigate to the `/checkout` page.
  2. Enter `"STUDENT10"` in the scholarship code field.
  3. Click "Apply".
* **Expected Result:** A discount of ₹4500 displays, and the grand total updates to ₹40500.

---

#### TC 3: Invalid Scholarship Code
* **Test Case ID:** TC-FEES-03
* **Title:** Verify validation error when applying an invalid scholarship code.
* **Preconditions:** Student has a pending invoice of ₹45000.
* **Execution Steps:**
  1. Enter `"INVALIDCODE"` in the scholarship code field.
  2. Click "Apply".
* **Expected Result:** Total remains unchanged, and error message `"Invalid or expired code."` displays.

---

#### TC 4: Boundary Roll Number Validation (BVA)
* **Test Case ID:** TC-FEES-04
* **Title:** Verify roll number field limits using Boundary Value Analysis.
* **Preconditions:** Student is on the checkout page.
* **Execution Steps:**
  1. Enter Roll Number `"1000"` (Invalid BVA boundary check). Verify error message: `"Roll number must be between 1001 and 9999"`.
  2. Enter Roll Number `"10000"` (Invalid BVA boundary check). Verify error message: `"Roll number must be between 1001 and 9999"`.
  3. Enter Roll Number `"1001"` (Valid BVA boundary check). Verify entry is accepted.
* **Expected Result:** Boundaries `1000` and `10000` are rejected, while `1001` is accepted.

---

#### TC 5: Missing Paytm UPI Field Validation
* **Test Case ID:** TC-FEES-05
* **Title:** Verify error validation when leaving the Paytm UPI ID field empty.
* **Preconditions:** Student has entered a valid roll number.
* **Execution Steps:**
  1. Enter valid roll number `"1005"`.
  2. Leave the "Paytm UPI ID" field blank.
  3. Click "Submit Payment".
* **Expected Result:** Form submission is blocked, and input validation warning `"UPI ID is required."` displays.
