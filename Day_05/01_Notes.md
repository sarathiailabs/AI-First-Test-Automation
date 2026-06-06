# Day 5: Software Testing Fundamentals

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| SDLC (Software Development Life Cycle) | 10 mins |
| STLC (Software Testing Life Cycle) | 10 mins |
| Agile & Scrum | 10 mins |
| QA Roles & Responsibilities | 5 mins |
| Functional Testing | 5 mins |
| Regression Testing | 5 mins |
| Smoke Testing | 5 mins |
| Sanity Testing | 5 mins |
| Integration Testing | 5 mins |
| System Testing | 5 mins |
| UAT (User Acceptance Testing) | 5 mins |
| Defect Lifecycle | 10 mins |
| Severity vs Priority | 10 mins |
| Root Cause Analysis | 5 mins |
| Requirement Analysis | 5 mins |
| Acceptance Criteria | 5 mins |
| Test Scenario Writing | 5 mins |
| Test Case Design | 10 mins |
| Exploratory Testing | 5 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Map testing activities directly to SDLC and STLC phases.
* Operate effectively within Agile/Scrum team frameworks.
* Differentiate between testing types (Regression, Smoke, Sanity, Integration, System, UAT).
* Report, categorize, and track software bugs through the entire Defect Lifecycle.
* Write clear, professional test cases and test scenarios from business requirements.

---

## Introduction

Before writing single-line automation scripts in Playwright, you must understand the fundamentals of software testing. Automated test suites are only as good as the manual test strategies behind them. Understanding why we test, how software is built (SDLC), how testing cycles operate (STLC), and how bugs are tracked ensures that your automated tests target high-value user paths.

In this module, we will demystify testing processes, sprint ceremonies, bug classification systems (Severity vs. Priority), and test case design methodologies. This knowledge forms the baseline for designing intelligent automation frameworks.

---

## SDLC (Software Development Life Cycle)

### Definition
The **Software Development Life Cycle (SDLC)** is a structured engineering process used by software teams to plan, design, build, test, and deploy high-quality software systems. *(Software banane, design karne, test aur deploy karne ka systematic sequence.)*

### Key Concepts
* **Phase Progression:** SDLC defines clear milestones from customer requirements to production releases.
* **Core Phases:**
  1. *Requirement Analysis:* Gathering business needs (what to build).
  2. *Design:* Designing system architecture and database layouts.
  3. *Implementation (Coding):* Developers writing source code.
  4. *Testing:* QA verifying the code against requirements.
  5. *Deployment:* Releasing the application to servers.
  6. *Maintenance:* Monitoring performance and fixing production bugs.

### Visual Explanation
```text
┌──────────────┐    ┌────────┐    ┌────────┐    ┌─────────┐    ┌────────────┐
│ Requirements ├───►│ Design ├───►│ Coding ├───►│ Testing ├───►│ Deployment │
└──────────────┘    └────────┘    └────────┘    └─────────┘    └────────────┘
```

### Example
#### Scenario
Building a new online fee payment portal for VJTI Mumbai.
* *Planning:* Product managers decide to accept credit cards and UPI payments.
* *Design:* Database architects design safe structures to store transactions.
* *Coding:* Developers write APIs to process UPI payments.
* *Testing:* QA validates transactions succeed.
* *Deployment:* Released to students on the college website.

### Real World Usage
Understanding SDLC tells QA when to prepare test plans (Design phase) and when to start executing test cases (Coding/Testing phase).

### Common Mistakes
* **Starting testing too late:** If QA only gets involved in the Testing phase, bugs discovered during design become extremely expensive to fix.

### Topic Summary
SDLC is the master roadmap for software creation. Early QA involvement reduces overall project costs.

---

## STLC (Software Testing Life Cycle)

### Definition
The **Software Testing Life Cycle (STLC)** is a systematic sequence of testing activities executed during the development lifecycle to verify that software meets quality standards. *(Software ko test karne ke steps jo SDLC ke parallel chalte hain.)*

### Key Concepts
* **Entry and Exit Criteria:** Each STLC phase has strict conditions that must be met before starting (Entry) and finishing (Exit).
* **Core Phases:**
  1. *Requirement Analysis:* Reviewing feature descriptions.
  2. *Test Planning:* Scoping resources, timelines, and tools.
  3. *Test Case Development:* Creating test scenarios and detailed test cases.
  4. *Test Environment Setup:* Configuring testing servers (Staging).
  5. *Test Execution:* Running tests, logging bugs, and executing automation.
  6. *Test Cycle Closure:* Reviewing metrics and publishing test summary reports.

### Visual Explanation
```text
Requirement ──► Planning ──► Case Design ──► Env Setup ──► Execution ──► Closure
```

### Example
#### Scenario
Running a testing cycle for a student login page.
* *Requirement Analysis:* Check if login requires roll number and birthdate.
* *Test Planning:* Allocate 2 QAs for 3 days of testing.
* *Case Design:* Write step-by-step checks for valid/invalid inputs.
* *Execution:* Run checks on Chrome/Firefox and report errors.
* *Closure:* Generate a report: 15 checks passed, 2 failed.

### Real World Usage
STLC ensures QA operations are structured and measurable, producing test summary reports at completion.

### Common Mistakes
* **Executing tests without environment verification:** If the test environment is unstable or contains invalid database records, tests will fail falsely.

### Topic Summary
STLC provides a structured framework for testing activities, running parallel to the SDLC.

---

## Agile & Scrum

### Definition
**Agile** is a software development philosophy that promotes iterative changes and collaboration, while **Scrum** is a specific framework implementing Agile through fixed-length cycles called Sprints. *(Chote-chote cycles (Sprints) me kaam karne aur deliver karne ka process.)*

### Key Concepts
* **Sprints:** 1-to-4 week intervals during which working software is built and tested.
* **Scrum Roles:** Product Owner (prioritizes backlog), Scrum Master (removes blockers), Developers/QAs.
* **Scrum Ceremonies:**
  * *Sprint Planning:* Choosing user stories to complete in the upcoming sprint.
  * *Daily Standup:* A 15-minute daily alignment check (what did I do, what will I do, any blockers).
  * *Sprint Review:* Demoing completed working features to stakeholders.
  * *Sprint Retrospective:* Reflecting on what went well and how the team can improve.

### Visual Explanation
```text
┌──────────────────────────────────────────────┐
│  Backlog ──► Sprint Planning ──► 2-Week Sprint│ ──► Standup (Daily) ──► Retrospective
└──────────────────────────────────────────────┘
```

### Example
#### Scenario
A college team organizing a 2-week technical festival:
* *Sprint Planning:* Choose tasks (book speakers, buy prizes, launch site).
* *Daily Standup:* A quick daily meeting: "I booked the auditorium, but need help with sponsor details."
* *Retrospective:* Discussing what went wrong: "We ordered banners too late; next year we must do it on Day 2."

### Real World Usage
Modern QAs write and execute automated test scripts inside cross-functional Agile sprints, testing user stories as developers commit code.

### Common Mistakes
* **Treating Scrum as mini-waterfall:** Waiting until Day 9 of a 10-day sprint to start testing. This causes bottlenecks and delays releases.

### Topic Summary
Agile/Scrum values collaboration and fast iterations. QA tests early and often within sprints.

---

## QA Roles & Responsibilities

### Definition
A **QA Engineer** is responsible for ensuring the overall quality of software deliverables by validating requirements, designing test strategies, and finding defects. *(Software ki quality maintain karne aur bugs ko prevent karne ka role.)*

### Key Concepts
* **Quality Assurance vs. Quality Control:**
  * *QA:* Focuses on preventing defects (process-focused, e.g., reviewing requirements).
  * *QC:* Focuses on finding defects (product-focused, e.g., executing test cases).
* **Key Tasks:** Analyzing requirements, writing test cases, executing tests, filing bug reports, verifying fixes, and writing automation scripts.

### Visual Explanation
```text
QA (Process Improvement, Prevention)  ◄──►  QC (Product Testing, Bug Hunting)
```

### Example
#### Scenario
* *QA activity:* Reviewing the fees portal requirements to make sure error messages are clearly defined.
* *QC activity:* Clicking the payment button to verify the "UPI Timeout" error message actually appears.

### Real World Usage
QAs act as quality advocates, preventing bugs before they reach production servers.

### Common Mistakes
* **Thinking QA is only about bug hunting:** QA also involves validating user requirements and streamlining release processes.

### Topic Summary
QA is a broad discipline aimed at process improvement and bug prevention, while QC is the execution phase of finding bugs.

---

## Functional Testing

### Definition
**Functional testing** is a black-box testing type that validates software actions against the specified requirements or user stories. *(Requirements ke hisab se software ke features sahi kaam kar rahe hain ya nahi, check karna.)*

### Key Concepts
* **Black-Box:** Testing the user interface (UI) or API endpoints without inspecting the internal source code.
* **Focus:** Validates inputs, outputs, processes, and UI interactions (e.g., form submissions).

### Visual Explanation
```text
Input (Valid credentials) ──► [ Student ERP Portal ] ──► Output (Dashboard loads)
```

### Example
#### Scenario
Testing the college catalog search field. Enter `"Data Structures"` and verify that search results display only computer science textbook items.

### Real World Usage
Most automated Playwright scripts are functional tests that simulate browser interactions.

### Common Mistakes
* **Assuming UI visual checks cover functionality:** A page may look correct but still fail to send form data to the database server.

### Topic Summary
Functional testing verifies *what* the system does based on requirements.

---

## Regression Testing

### Definition
**Regression testing** is the process of re-running test cases to ensure that code changes or updates have not broken existing, working features. *(Naya code aane se purana chalta hua feature na tut jaye, yeh double check karna.)*

### Key Concepts
* **Unintended Impact:** Verifies new code hasn't caused "regressions" in old code.
* **Automation-Friendly:** Because regression tests are repeated frequently, they are ideal candidates for automation.

### Visual Explanation
```text
New Feature Added ──► Run Regression Suite ──► Ensure baseline features still pass
```

### Example
#### Scenario
Adding a discount coupon feature to a hostel booking portal. QA runs regression tests on credit card payments to verify payments still work.

### Real World Usage
Playwright suites are typically run on CI/CD pipelines as regression checks before merging code changes.

### Common Mistakes
* **Running the entire test suite every time:** If the suite is massive, run a targeted subset first to save time.

### Topic Summary
Regression testing protects existing features from breaking when code updates are introduced.

---

## Smoke Testing

### Definition
**Smoke testing** (build verification testing) is a quick suite of tests run on a new software build to verify that its critical core features work. *(Nayi build aane par uske basic major features check karna taaki build reject ya accept ho sake.)*

### Key Concepts
* **Showstopper Check:** Assesses if the build is stable enough for deeper testing.
* **Execution Time:** Usually completed in less than 15 minutes.

### Visual Explanation
```text
New Build ──► Run Smoke Suite (5-10 tests) ──► Pass? (Continue testing) OR Fail? (Reject build)
```

### Example
#### Scenario
Buying a new motorcycle. Before taking it on a long trip, you turn the key, start the engine, check the headlights, and verify the brakes work. If the engine doesn't start, you reject it immediately.

### Real World Usage
Smoke tests run automatically on push events to verify the deployment environment is operational.

### Common Mistakes
* **Including deep edge-case validation:** Smoke tests should only verify critical paths. Save edge cases for functional cycles.

### Topic Summary
Smoke testing verifies basic build stability. If it fails, the build is rejected.

---

## Sanity Testing

### Definition
**Sanity testing** is a quick, focused test suite run on a stable build to verify that a specific bug fix or minor change works as expected. *(Kisi specific bug fix ya minor change ko target karke quick check karna.)*

### Key Concepts
* **Targeted Verification:** Unlike smoke testing (which covers the whole build), sanity testing focuses on a specific feature area.
* **Unscripted:** Often performed quickly without detailed test scripts.

### Visual Explanation
```text
Bug Fixed in Search Module ──► Sanity Test Search UI ──► Verify Search works
```

### Example
#### Scenario
A bug where the search bar crashed on entering special characters is fixed. QA runs a sanity check on the search bar with inputs like `"$%#"` to verify it no longer crashes.

### Real World Usage
Used to verify hotfixes before releasing them to production servers.

### Common Mistakes
* **Confusing smoke and sanity testing:** Smoke tests check broad build stability; sanity tests check specific, narrow changes.

### Topic Summary
Sanity testing is a focused check of specific bug fixes or minor updates.

---

## Integration Testing

### Definition
**Integration testing** verifies that different modules, components, or services within an application interact correctly. *(Separate code modules ya APIs aapas me sahi connect hokar communicate kar rahe hain ya nahi, verify karna.)*

### Key Concepts
* **Interactions:** Focuses on data transfer between systems (e.g. UI communicating with an API).
* **Approaches:** Top-Down, Bottom-Up, Big Bang.

### Visual Explanation
```text
[ Fees Page UI ] ──── (API Request / DB Call) ────► [ College Database Server ]
```

### Example
#### Scenario
Verifying that entering payment details in the fee portal UI successfully calls the Paytm UPI API and writes the transaction to the college database.

### Real World Usage
Testing API payloads and contract configurations between microservices.

### Common Mistakes
* **Testing modules in isolation:** Unit tests check modules in isolation; integration tests must test them together.

### Topic Summary
Integration testing verifies that separate system modules communicate correctly.

---

## System Testing

### Definition
**System testing** is an end-to-end testing type that validates the complete, integrated software package against its system specifications. *(Pure software package ka end-to-end check up karna.)*

### Key Concepts
* **End-to-End (E2E):** Validates the entire user flow from start to finish.
* **Black-Box:** Validates system inputs and outputs without looking at internal code.

### Visual Explanation
```text
Start ──► User Login ──► Search ──► Checkout ──► Payment ──► Receipt Generated ──► End
```

### Example
#### Scenario
A student logs in to the fee portal, searches for their pending fee invoice, completes payment, and receives a downloadable PDF receipt via email.

### Real World Usage
Validating staging or pre-production builds before release.

### Common Mistakes
* **Skipping backend checks:** E2E testing must verify database updates and email notifications, not just UI state.

### Topic Summary
System testing validates the entire application flow to ensure it works as a cohesive unit.

---

## UAT (User Acceptance Testing)

### Definition
**User Acceptance Testing (UAT)** is the final phase of testing where real users or clients validate the software against their business needs before it goes live. *(Real users ya clients dwara software ko pass/fail karna before release.)*

### Key Concepts
* **Business Fit:** Verifies that the software solves the actual business problems it was designed for.
* **Beta/Alpha testing:** Often conducted by business analysts, product owners, or beta customers.

### Visual Explanation
```text
Staging Build ──► Business User Testing ──► Signoff / Approval ──► Production Release
```

### Example
#### Scenario
Before launching the new fee portal, the college registrar and a group of student representatives test it to ensure it matches their daily receipting workflows.

### Real World Usage
Gathering feedback from beta users before a major public product launch.

### Common Mistakes
* **Treating UAT as a technical bug hunt:** UAT should focus on user experience and business workflows, not technical edge cases.

### Topic Summary
UAT is the final gatekeeper phase where business stakeholders verify the software is ready for production.

---

## Defect Lifecycle

### Definition
The **Defect Lifecycle** (Bug Lifecycle) is the sequence of states a software bug transitions through from its initial discovery to its final resolution. *(Bug milne se lekar uske closed hone tak ke saare stages.)*

### Key Concepts
* **States:**
  * *New:* Bug found and logged.
  * *Assigned:* Assigned to a developer.
  * *Open:* Developer actively troubleshooting.
  * *Fixed:* Developer commits fix.
  * *Retest:* QA verifying fix.
  * *Verified:* Fix confirmed by QA.
  * *Closed:* Resolved and closed.
  * *Rejected/Duplicate/Deferred:* Dismissed or postponed.

### Visual Explanation
```text
New ──► Assigned ──► Open ──► Fixed ──► Retest ──► Verified ──► Closed
                               │
                               └──► Rejected / Duplicate / Deferred
```

### Example
#### Scenario
* **New:** QA logs a bug: payment portal crashes when card number is entered.
* **Fixed:** Developer fixes the validation script.
* **Retest:** QA tests payment again.
* **Closed:** QA confirms payment works and closes the ticket.

### Real World Usage
Using tracking tools like Jira or GitHub Issues to manage development workflows.

### Common Mistakes
* **Closing a bug without retesting:** Always retest the bug in the target environment before closing it.

### Topic Summary
The Defect Lifecycle tracks a bug's progress from discovery to resolution, keeping teams aligned.

---

## Severity vs Priority

### Definition
* **Severity** refers to the technical impact of a defect on the software's operation. *(Bug software ko kitna affect karta hai (Severity).)*
* **Priority** refers to the business urgency of fixing the defect. *(Bug ko kitna jaldi fix karna hai (Priority).)*

### Key Concepts
* **Severity Levels:** Blocker, Critical, Major, Minor.
* **Priority Levels:** High, Medium, Low.
* **Combinations:**
  * *High Severity, High Priority:* Fee payment button crashes the portal.
  * *High Severity, Low Priority:* Portal crashes when clicking a legacy link that users rarely visit.
  * *Low Severity, High Priority:* Typos in the college name on the login header.
  * *Low Severity, Low Priority:* A minor alignment mismatch in the page footer.

### Visual Explanation
| | High Priority | Low Priority |
| --- | --- | --- |
| **High Severity** | Payment Crash | Edge-Case Crash |
| **Low Severity** | Portal Header Typo | Footer Alignment |

### Example
#### Scenario
* *Scenario A:* The registration submit button is missing.
  * **Classification:** High Severity (users can't register), High Priority (blocks registrations).
* *Scenario B:* A typo in the privacy policy page says "impliment" instead of "implement".
  * **Classification:** Low Severity (text typo), Low Priority (minor page, doesn't impact usage).

### Real World Usage
Used during bug triage meetings to decide which bugs developers should fix first.

### Common Mistakes
* **Marking every bug as High Priority:** Over-classifying bugs leads to triage bottlenecks and developer fatigue.

### Topic Summary
Severity measures technical impact. Priority measures business urgency. Both are needed to triage bugs effectively.

---

## Root Cause Analysis

### Definition
**Root Cause Analysis (RCA)** is a problem-solving process used to identify the underlying cause of a defect to prevent it from reoccurring. *(Bug ki asli wajah tak pahunch kar use permanent solve karna.)*

### Key Concepts
* **Preventative:** Focuses on fixing the system process rather than just the symptom.
* **Five Whys:** Continually asking "Why?" to drill down to the source of the problem.

### Visual Explanation
```text
Symptom: Payment failed ──► Why? (Out of memory) ──► Why? (Memory leak in API) ──► Root Cause (Unhandled db connection cleanup)
```

### Example
#### Scenario
Outage: Students could not pay fees online.
* *Why?* Database was locked.
* *Why?* Connection pool exhausted.
* *Why?* Connections weren't closing.
* *Why?* Unhandled exceptions in the payment callback bypassed the database cleanup block. (Root Cause)

### Real World Usage
Writing post-mortem incident reports after major production outages.

### Common Mistakes
* **Blaming individuals:** Focus on process failures, not individual developer mistakes.

### Topic Summary
RCA identifies the root cause of a defect to implement permanent fixes and prevent future occurrences.

---

## Requirement Analysis

### Definition
**Requirement Analysis** is the process of reviewing business requirement documents (BRD) or user stories to ensure they are complete, testable, and unambiguous. *(User stories aur requirements ko test-friendly aur clear banana.)*

### Key Concepts
* **Testability:** Can a requirement be verified with a pass/fail test?
* **Completeness:** Are all edge cases and error states defined?
* **Ambiguity:** Avoiding vague terms like "fast" or "user-friendly".

### Visual Explanation
```text
Vague: "Portal loads fast" ──► Requirement Analysis ──► Testable: "Portal load time < 2 seconds"
```

### Example
#### Scenario
* *Vague Requirement:* "Students must enter a secure roll number."
* *Analyzed Requirement:* "Roll numbers must be a 4-digit number between 1001 and 9999."

### Real World Usage
Reviewing stories during sprint planning to ensure they meet the Definition of Ready (DoR).

### Common Mistakes
* **Accepting requirements without review:** Testing untested assumptions leads to bugs late in the development cycle.

### Topic Summary
Requirement Analysis ensures feature specifications are clear, testable, and realistic before development begins.

---

## Acceptance Criteria

### Definition
**Acceptance Criteria (AC)** are the specific requirements and boundary conditions that a user story must meet to be accepted by the Product Owner. *(Kisi ticket ko 'Done' declare karne ke boundary rules.)*

### Key Concepts
* **Definition of Done:** Defines the boundaries of a feature.
* **Gherkin Format (Given-When-Then):** Often used to write testable scenarios:
  * *Given:* Preconditions.
  * *When:* User action.
  * *Then:* Expected outcome.

### Visual Explanation
```text
User Story ──► Defines feature value
  └─ Acceptance Criteria ──► Boundaries & Pass/Fail rules
```

### Example
#### Scenario
User Story: Reset Password.
* *AC 1:* User enters email and clicks reset.
* *AC 2:* System sends a secure reset link valid for 1 hour.
* *AC 3:* Showing error if email is not found in database.

### Real World Usage
Developers write code to pass the AC, while QAs design test cases to verify them.

### Common Mistakes
* **Writing vague criteria:** Statements like "System should work correctly" are not testable. Be specific about expected behaviors.

### Topic Summary
Acceptance Criteria define the boundaries of a feature, serving as the blueprint for both development and testing.

---

## Test Scenario Writing

### Definition
A **Test Scenario** is a high-level classification of what to test, summarizing a user workflow into a single check statement. *(High-level overview ki 'kya test karna hai'.)*

### Key Concepts
* **Scenario vs. Test Case:**
  * *Test Scenario:* Broad definition ("Verify checkout payment").
  * *Test Case:* Step-by-step validation ("Verify checkout with visa card", "Verify checkout with invalid card").
* **Coverage:** Ensures all user workflows are mapped to testing requirements.

### Visual Explanation
```text
Test Scenario (What to test): Verify Login Page
  ├─ Test Case 1 (How to test): Valid Login
  ├─ Test Case 2 (How to test): Invalid Password
  └─ Test Case 3 (How to test): Empty Input Fields
```

### Example
#### Scenario
E-commerce search module.
* *Scenario 1:* Verify search works with matching terms.
* *Scenario 2:* Verify search handles special characters.
* *Scenario 3:* Verify search displays pagination controls.

### Real World Usage
Creating high-level test maps to estimate QA resources and get sign-off from stakeholders.

### Common Mistakes
* **Confusing scenarios and cases:** Writing step-by-step verification instructions inside high-level scenarios makes them difficult to maintain.

### Topic Summary
Test scenarios define *what* to test, acting as the parent categories for detailed test cases.

---

## Test Case Design

### Definition
A **Test Case** is a detailed set of preconditions, execution steps, test data, and expected results designed to verify a specific software behavior. *(Step-by-step documentation ki input aur expected output ke sath 'kaise test karna hai'.)*

### Key Concepts
* **Components:** Test Case ID, Title, Preconditions, Steps, Test Data, Expected Result, Actual Result, Status (Pass/Fail).
* **Design Techniques:**
  * *Boundary Value Analysis (BVA):* Testing boundary limits (e.g. testing inputs `1000`, `1001`, `9999`, `10000` for a 4-digit roll number between 1001 and 9999).
  * *Equivalence Partitioning (EP):* Grouping input data into valid and invalid classes to reduce the number of test cases.

### Visual Explanation
```text
Test Case Structure:
┌──────────────────────────────────────────────────────────────┐
│ ID: TC01 | Title: Validate Login | Data: rahul@vjti.ac.in     │
├──────────────────────────────────────────────────────────────┤
│ Steps: 1. Enter Email | 2. Enter Password | 3. Click Submit  │
├──────────────────────────────────────────────────────────────┤
│ Expected Result: Dashboard loads successfully.               │
└──────────────────────────────────────────────────────────────┘
```

### Example
#### Test Case Design (BVA)
* **ID:** TC-AUTH-01
* **Title:** Register student roll number.
* **Preconditions:** Roll number field is active.
* **Steps:**
  1. Navigate to `/register`.
  2. Enter `"1000"` (Invalid BVA boundary check).
  3. Verify validation error.
  4. Enter `"1001"` (Valid BVA boundary check).
  5. Verify successful entry.
* **Expected Result:** Roll number 1000 shows error; 1001 succeeds.

### Real World Usage
Writing test cases in test management tools (like TestRail) to organize regression runs.

### Common Mistakes
* **Omitting preconditions or expected results:** This makes it difficult for other testers to run the cases consistently.

### Topic Summary
Test cases are detailed instructions that verify specific software inputs and outputs.

---

## Exploratory Testing

### Definition
**Exploratory testing** is a hands-on, unscripted testing approach where test design and test execution occur simultaneously, relying on the tester's intuition and experience. *(Bina kisi script ke product ko investigate aur explore karke bug dhoondhna.)*

### Key Concepts
* **Ad-hoc Testing:** Minimal documentation. Testers explore the application dynamically to find unexpected bugs.
* **Timeboxed Sessions:** Often run as focused "charters" (e.g. 1 hour exploring payment error recovery).

### Visual Explanation
```text
Explore System ──► Learn Behavior ──► Design Test ──► Execute ──► Repeat
```

### Example
#### Scenario
Testing a chat application. Instead of following a script, the tester attempts to send massive blocks of text, drop connection mid-send, or paste emojis to see if it crashes.

### Real World Usage
Executing quick test passes on new builds to find edge-case bugs that structured test cases miss.

### Common Mistakes
* **Lack of tracking:** If you don't document the steps you took, it will be difficult to reproduce any bugs you find.

### Topic Summary
Exploratory testing relies on active learning and exploration, complementing structured test cases to find edge-case bugs.

---

## Session Summary

### Key Takeaways
1. **Lifecycle Alignment:** SDLC guides the overall software project, while STLC structures the QA process.
2. **Testing Types:** Smoke tests check basic build stability; regression tests verify changes haven't broken existing features; system tests check end-to-end user flows.
3. **Bug Management:** Severity measures technical impact; priority measures business urgency. Bug triages use both metrics to prioritize developer tasks.
4. **Test Design:** Requirements are translated into Acceptance Criteria (AC), which are mapped to Test Scenarios (what to test) and written as detailed Test Cases (how to test).

### Important Interview Points
* **What is the difference between Smoke and Sanity testing?**
  * Smoke testing checks build stability across all critical core paths. Sanity testing is a focused check on specific bug fixes or minor code changes.
* **Can a bug be High Severity but Low Priority? Give an example.**
  * Yes. A bug that crashes the app on an obscure legacy page that users rarely visit. It is High Severity (crash) but Low Priority (rarely accessed).
* **What is the difference between Verification and Validation?**
  * Verification checks if the product is built correctly according to requirements (process-focused). Validation checks if the product meets customer expectations (user-focused).

### Quick Revision Sheet

* **Defect Lifecycle states:**
  `New ──► Assigned ──► Open ──► Fixed ──► Retest ──► Verified ──► Closed`
* **Test Case Components:**
  ID, Title, Preconditions, Steps, Test Data, Expected Result, Status.
* **Agile Sprint Steps:**
  Backlog Planning ──► Sprint Iteration (Development/Testing) ──► Review ──► Retrospective.
