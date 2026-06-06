# Day 5: Software Testing Fundamentals – Student Revision Notes

This revision document is designed to help you quickly review the key concepts taught during the Day 5 Software Testing Fundamentals lecture session.

---

## SDLC (Software Development Life Cycle)

### Definition
A structured process used by software engineering teams to design, develop, test, and maintain software products. *(Software banane, design karne, test aur deploy karne ka systematic sequence.)*

### Example
Building a student registration feature on a college ERP: gathering requirements (DoB, roll number, name), database schema design, writing registration APIs, testing, and deploying to production.

### Key Points
* Minimizes development costs and improves release quality.
* Phases: Requirements ──► Design ──► Coding ──► Testing ──► Deployment ──► Maintenance.

### Common Mistakes
* **Starting testing too late:** Postponing QA involvement until the coding phase is complete, which makes bug fixes significantly more expensive.

---

## STLC (Software Testing Life Cycle)

### Definition
A structured testing process consisting of specific phases designed to verify that software meets quality standards. *(Software ko test karne ke steps jo SDLC ke parallel chalte hain.)*

### Example
A QA team reviews new UPI payment user stories, writes test cases, runs execution tests on the staging portal, and logs database connection bugs.

### Key Points
* Includes entry and exit criteria for each phase to ensure structure.
* Phases: Requirement Analysis ──► Planning ──► Case Design ──► Environment Setup ──► Execution ──► Cycle Closure.

### Common Mistakes
* **Executing tests without verifying environments:** Running tests on unstable college servers, leading to false bug reports.

---

## Agile & Scrum

### Definition
Agile is a development methodology emphasizing collaboration and fast iterations; Scrum is a framework implementing Agile using fixed-length iterations called Sprints. *(Chote-chote cycles (Sprints) me kaam karne aur deliver karne ka process.)*

### Example
A college tech fest planning committee holding a 2-week sprint planning session, running daily standup alignments, and holding a retrospective on Day 10.

### Key Points
* Focused on delivering working software in 2-week sprints.
* Scrum ceremonies: Sprint Planning, Daily Standup, Review, Retrospective.

### Common Mistakes
* **Treating sprints as mini-waterfall cycles:** Postponing all testing activities until the final two days of a sprint.

---

## QA Roles & Responsibilities

### Definition
QA engineers design quality processes to prevent defects, while QC testers execute test cases to identify bugs in software. *(Software ki quality maintain karne aur bugs ko prevent karne ka role.)*

### Example
QA reviews user stories during sprint planning to ensure error messages are defined (prevention); QC runs tests to check if those errors appear (detection).

### Key Points
* QA = Process-focused and preventative.
* QC = Product-focused and reactive (testing).

### Common Mistakes
* **Assuming QA is only about running test cases:** Forgetting that QA also involves improving development processes to prevent bugs early on.

---

## Functional Testing

### Definition
A black-box testing type that validates system features against their requirements. *(Requirements ke hisab se software ke features sahi kaam kar rahe hain ya nahi, check karna.)*

### Example
Entering `"1005"` (roll number) and `"rahul@paytm"` (UPI ID) in a fee form to verify the success screen loads.

### Key Points
* Black-box testing: QA verifies inputs and outputs without looking at source code.
* Focuses on what the system does.

### Common Mistakes
* **Assuming UI visual correctness means functional success:** Thinking that because a form looks correct, the backend database is updating correctly.

---

## Regression Testing

### Definition
Re-testing existing features to ensure that new code updates have not broken previous functionality. *(Naya code aane se purana chalta hua feature na tut jaye, yeh double check karna.)*

### Example
Adding Paytm UPI support and running test cases on credit card payments to verify credit card checkouts still work.

### Key Points
* Verifies new code has not caused regressions.
* Ideal candidate for automated test runs.

### Common Mistakes
* **Running the entire regression suite for minor changes:** Choose a targeted subset of test cases to save execution time.

---

## Smoke Testing

### Definition
A rapid test suite run on a new build to verify that critical core paths work. *(Nayi build aane par uske basic major features check karna taaki build reject ya accept ho sake.)*

### Example
Checking if a new motorcycle starts, moves, and brakes before purchasing. Or verifying that a new student portal build launches and allows logins.

### Key Points
* Acts as a build verification test.
* If smoke tests fail, the build is immediately rejected.

### Common Mistakes
* **Including obscure edge cases in smoke runs:** Smoke tests should only verify critical paths.

---

## Sanity Testing

### Definition
A focused test suite run on a stable build to verify that a specific bug fix works. *(Kisi specific bug fix ya minor change ko target karke quick check karna.)*

### Example
Retesting the student search box with special characters after a developer deploys a fix for a search crash.

### Key Points
* Narrow, targeted, and usually unscripted verification.
* Focuses on specific feature changes.

### Common Mistakes
* **Confusing smoke and sanity tests:** Smoke checks broad build stability; sanity verifies narrow bug fixes.

---

## Integration Testing

### Definition
Testing the interactions and data transfers between different modules or services. *(Separate code modules ya APIs aapas me sahi connect hokar communicate kar rahe hain ya nahi, verify karna.)*

### Example
Verifying that submitting a student fee form correctly sends data to the Paytm UPI API and updates the college database.

### Key Points
* Focuses on data transfer interfaces.
* Prevents communication bugs between modules.

### Common Mistakes
* **Testing modules in isolation:** Unit tests check modules in isolation; integration tests must test them together.

---

## System Testing

### Definition
End-to-end testing that validates the complete, integrated software package as a unified system. *(Pure software package ka end-to-end check up karna.)*

### Example
Testing a complete student portal flow: login, search invoice, checkout, Paytm UPI payment processing, database writes, and email receipts.

### Key Points
* Fully black-box.
* Validates end-to-end user workflows.

### Common Mistakes
* **Ignoring backend states:** Only verifying UI changes while neglecting database updates or third-party API payloads.

---

## UAT (User Acceptance Testing)

### Definition
The final testing phase where business owners or real users validate that the software matches actual operational workflows. *(Real users ya clients dwara software ko pass/fail karna before release.)*

### Key Points
* Verifies business fit before launching to production.
* Often called Alpha/Beta testing.

### Common Mistakes
* **Treating UAT as a technical bug hunt:** UAT should focus on user experience and business workflows, not technical edge cases.

---

## Defect Lifecycle

### Definition
The series of states a software bug transitions through from discovery to resolution. *(Bug milne se lekar uske closed hone tak ke saare stages.)*

### Example
A bug goes from **New** (logged) to **Assigned** (to developer), **Open** (debugging), **Fixed** (fix deployed), **Retest** (QA verification), and **Closed** (resolved).

### Key Points
* Tracks and manages defect tickets in tools like Jira.
* Only QA should close a bug after verifying the fix.

### Common Mistakes
* **Closing bug tickets without retesting:** Relying solely on developer confirmation without verification.

---

## Severity vs. Priority

### Definition
* **Severity:** The technical impact of a bug on system operations (determined by QA). *(Bug software ko kitna affect karta hai (Severity).)*
* **Priority:** The business urgency of fixing the bug (determined by the Product Owner). *(Bug ko kitna jaldi fix karna hai (Priority).)*

### Example
A typo in the college logo is **Low Severity** (doesn't break code) but **High Priority** (highly visible to students).

### Key Points
* High Severity / High Priority = Payment gateway crash.
* Low Severity / Low Priority = Alignment issue in footer.

### Common Mistakes
* **Classifying every bug as High Priority:** Overloading development queues, which causes triage bottlenecks.

---

## Root Cause Analysis

### Definition
A problem-solving methodology used to identify the underlying cause of a defect to prevent it from reoccurring. *(Bug ki asli wajah tak pahunch kar use permanent solve karna.)*

### Example
Determining that a fee payment crash was caused by an unhandled database connection timeout, rather than simply restarting the server.

### Key Points
* Focuses on process prevention rather than symptoms.
* Uses techniques like the "Five Whys".

### Common Mistakes
* **Blaming developers for bugs:** RCA should focus on improving testing and development processes, not assigning blame.

---

## Requirement Analysis

### Definition
Reviewing requirements or user stories to ensure they are complete, testable, and clear before coding begins. *(User stories aur requirements ko test-friendly aur clear banana.)*

### Example
Converting "The fees portal must load fast" to "The fees portal page load time must be under 2 seconds."

### Key Points
* Verifies that requirements are testable.
* Resolves ambiguities early in the development cycle.

### Common Mistakes
* **Accepting vague criteria:** Proceeding to design tests based on ambiguous requirements.

---

## Acceptance Criteria

### Definition
The specific boundaries and conditions a user story must satisfy to be accepted by the product owner. *(Kisi ticket ko 'Done' declare karne ke boundary rules.)*

### Example
"The system must send a confirmation email containing a downloadable PDF fee receipt within 5 minutes of successful payment."

### Key Points
* Serves as the blueprint for feature acceptance.
* Often written in Given-When-Then format.

### Common Mistakes
* **Writing vague criteria:** Statements like "System should work correctly" are not testable. Be specific about expected behaviors.

---

## Test Scenario Writing

### Definition
A high-level summary of what to test, mapping a complete user workflow. *(High-level overview ki 'kya test karna hai'.)*

### Example
"Verify student fee payment with Paytm UPI ID."

### Key Points
* Represents parent categories for detailed test cases.
* Used to estimate test coverage.

### Common Mistakes
* **Writing step-by-step instructions inside scenarios:** Keep scenarios high-level; use test cases for detailed steps.

---

## Test Case Design

### Definition
A detailed set of steps, test data, and expected results designed to verify a specific software behavior. *(Step-by-step documentation ki input aur expected output ke sath 'kaise test karna hai'.)*

### Key Points
* Key fields: ID, Title, Steps, Test Data, Expected Result, Status.
* Uses techniques like Boundary Value Analysis (BVA).

### Common Mistakes
* **Leaving out preconditions or test data:** Makes it difficult for other testers to run the cases consistently.

---

## Exploratory Testing

### Definition
An unscripted testing approach where testers design and run tests dynamically based on their experience. *(Bina kisi script ke product ko investigate aur explore karke bug dhoondhna.)*

### Example
Intentionally pasting massive blocks of text into fields or disconnecting internet access mid-transaction to see how the app responds.

### Key Points
* Simultaneous learning, design, and execution.
* Finds edge-case bugs that structured tests miss.

### Common Mistakes
* **Failing to document steps:** Makes it difficult to reproduce any bugs discovered during exploratory testing.

---

## Assignment Summary

* **SDLC Phase Mapping:** Identified the correct SDLC phases for VJTI student fees portal development tasks.
* **STLC Process Mapping:** Mapped Entry/Exit criteria and QA deliverables (RTM, Test Plan, Test Cases, Defect Log) to each STLC phase.
* **Bug Report Design:** Drafted a professional bug report containing preconditions, steps, actual/expected results for a roll number crash, and tracked its lifecycle transitions.
* **Severity vs. Priority Matrix:** Classified different bug scenarios (e.g., Paytm UPI gateway failure, dashboard logo spelling typo) using severity and priority scales.
* **5 Fees Checkout Test Cases:** Created 5 detailed functional and boundary test cases (happy path, valid promo `STUDENT10`, invalid promo, roll number BVA, missing UPI ID validation) with step-by-step instructions.

---

## Quick Revision Sheet

* **SDLC Phases:**
  `Requirements ──► Design ──► Coding ──► Testing ──► Deployment ──► Maintenance`
* **Defect Lifecycle states:**
  `New ──► Assigned ──► Open ──► Fixed ──► Retest ──► Verified ──► Closed`
* **Severity vs. Priority Matrix:**
  * Blocker/High = Paytm Gateway crash.
  * Minor/High = Dashboard spelling typo.
  * Blocker/Low = Legacy browser crash.
  * Minor/Low = Footer rules link blank.

---

## Important Takeaways

1. Automated test suites are only as good as the manual test scenarios they verify.
2. SDLC guides overall product development, while STLC organizes QA activities.
3. Bug severity is determined by technical impact (QA), while priority is based on business urgency (Product Owner).
4. Good test cases contain clear preconditions, step-by-step instructions, and expected results, making them easy to automate later in Playwright.
5. Exploratory testing complements scripted testing by finding edge-case bugs through dynamic user scenarios.
