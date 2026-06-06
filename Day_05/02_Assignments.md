# Day 5: Software Testing Fundamentals – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of software testing cycles, defect management, and test case design methodologies. Please attempt all questions.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the primary difference between the Software Development Life Cycle (SDLC) and the Software Testing Life Cycle (STLC)? How do they interact during an Agile sprint, and why is "Shift Left" testing recommended?

### Question 2 🔥 **Frequently Asked**
Describe the typical phases of the Defect/Bug Lifecycle. What are the key criteria that must be met before QA can change a bug status from "Fixed" to "Closed"? Under what conditions would a bug status be set to "Deferred" or "Rejected"?

### Question 3 📌 **Important**
Explain the concepts of **Severity** and **Priority** in defect tracking. Provide a brief example of a bug that is classified as **Low Severity but High Priority**, and another that is **High Severity but Low Priority**.

### Question 4 📌 **Important**
How do Smoke Testing and Sanity Testing differ? In a CI/CD automation pipeline, which of the two testing types is typically run first and why?

### Question 5 💡 **Good to Know**
What is the purpose of **Acceptance Criteria** in a user story? Contrast this with the purpose of a **Test Case**. Can a user story have multiple test cases for a single acceptance criterion?

---

## Practical Assignments

### Assignment 1: Explain SDLC (Phase Mapping)

* **Interview Relevance:** Tests your fundamental understanding of software project workflows, ensuring you know where QA fits in the larger picture beyond writing test cases.
* **Difficulty Level:** Beginner
* **Concepts Covered:** SDLC Phases (Requirement Analysis, Design, Coding, Testing, Deployment, Maintenance).

#### Problem Statement
Below is a list of tasks performed by an engineering team building the VJTI Student Fees Portal. Identify which SDLC phase each task belongs to:
1. The Product Owner meets with college registrars to decide if the portal should support Paytm UPI payments.
2. The Database Administrator designs the database tables and schemas to store transaction tokens.
3. A QA Engineer runs automated scripts on the staging server to verify the registration page.
4. A Developer writes backend API endpoints using Node.js to register student profiles.
5. The Devops Engineer configures Docker containers and deploys the build to AWS production.

#### Requirements
For each task (1-5), write down the corresponding SDLC phase and explain why it belongs to that phase in 1-2 sentences.

---

### Assignment 2: Explain STLC (Process Deliverables)

* **Interview Relevance:** A classic interview question verifying if you understand the structural steps and deliverables of a professional testing lifecycle.
* **Difficulty Level:** Beginner-Intermediate
* **Concepts Covered:** STLC Phases, Deliverables, Entry/Exit Criteria.

#### Problem Statement
For each of the six phases of the STLC, map the primary document or artifact that the QA team is responsible for producing.

#### Requirements
1. Map the STLC phases:
   * Requirement Analysis
   * Test Planning
   * Test Case Development
   * Test Environment Setup
   * Test Execution
   * Test Cycle Closure
2. For each phase, write down:
   * **Input (Entry Criteria):** What QA needs before the phase can start.
   * **Key QA Deliverable:** The primary document produced during this phase.
   * **Output (Exit Criteria):** The conditions required to mark the phase complete.

---

### Assignment 3: Explain Bug Lifecycle (Defect Report)

* **Interview Relevance:** Tests your ability to write clear, reproducible bug reports — the primary communication tool between QAs and Developers.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Defect Lifecycle, Defect Report Writing, Reproducibility.

#### Problem Statement
A student reports that when paying fees on the portal with an empty roll number field, the system crashes to a raw database stack-trace screen instead of showing a validation error. You need to write a professional bug report and trace its lifecycle.

#### Requirements
1. **Draft a Bug Report** using the standard layout:
   * **Defect ID:** (e.g. BUG-FEES-01)
   * **Title:** Concise and descriptive summary of the bug.
   * **Severity & Priority:** Classify the issue.
   * **Preconditions:** Initial conditions required.
   * **Steps to Reproduce:** Step-by-step actions.
   * **Actual Result:** What the system actually did.
   * **Expected Result:** What the system should have done.
2. **Lifecycle Mapping:** Assume the developer writes a fix, and QA verifies it. List the state transitions the bug ticket goes through from creation to completion.

---

### Assignment 4: Severity vs. Priority Classification

* **Interview Relevance:** Tests analytical thinking and decision-making skills in a triage meeting to balance business urgency and technical severity.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Severity/Priority Matrix, Defect Triage.

#### Problem Statement
Review the following four software bug descriptions on the VJTI Student ERP. Classify each bug into one of the four quadrants of the Severity vs. Priority Matrix:
1. **Scenario A:** The college logo on the student dashboard portal has a typo: "VJTI Mumabi" instead of "VJTI Mumbai".
2. **Scenario B:** The Paytm UPI payment gateway crashes with a 500 server error when a user inputs a valid UPI ID.
3. **Scenario C:** The application crashes to a black screen only when a user attempts to change their password while using a legacy browser (e.g. Internet Explorer 11) which is no longer officially supported by the college IT cell.
4. **Scenario D:** On the student profile page, clicking the secondary "Rules and Regulations" link in the footer opens a blank page, but the main fee payments and academic results links work correctly.

#### Requirements
For each scenario (A-D), assign:
* **Severity level:** (Blocker, Major, Minor)
* **Priority level:** (High, Medium, Low)
* **Rationale:** A brief paragraph explaining your reasoning.

---

### Assignment 5: Create 5 Test Cases (Student Fees Checkout)

* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** Test Case Design, Functional Testing, Boundary Conditions, Paytm UPI validation.

#### Problem Statement
Design a test suite containing 5 detailed test cases for a fee payment checkout flow on the VJTI Student ERP website. The page contains:
* Student Roll Number (valid range: 1001 to 9999).
* An invoice detail showing a standard fee of ₹45000.
* A promo/scholarship code input field (valid code: `STUDENT10` gives 10% off).
* A Paytm UPI Payment form (requires a valid UPI ID like `user@okaxis` or `user@paytm`).
* A "Submit Payment" button.

#### Requirements
Write 5 distinct test cases covering:
1. **TC 1: Valid Checkout Flow** (Happy path: inputting valid roll number, valid UPI ID, submitting, verifying success screen).
2. **TC 2: Scholarship Code Application** (Applying valid code `STUDENT10` and verifying total updates to ₹40500).
3. **TC 3: Invalid Scholarship Code** (Applying an expired or invalid code, verifying error message).
4. **TC 4: Boundary Roll Number Validation** (Testing roll number field with boundaries like 1000 and 10000 using Boundary Value Analysis, verifying registration blocks).
5. **TC 5: Missing Paytm UPI Field Validation** (Submitting payment with UPI ID field blank, verifying validation error matches requirements).

Format each test case in a structured outline containing:
* Test Case ID & Title
* Preconditions
* Execution Steps
* Test Data
* Expected Result
