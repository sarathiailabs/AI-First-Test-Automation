# Day 17: AI for Test Automation + Career Preparation – Assignments

This assignment file contains theoretical questions and practical tasks designed to reinforce prompt engineering, self-healing selector scripts, and professional GitHub portfolio README templates.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is **Prompt Engineering**? Describe the role of "Constraints" and "Role Prompting" when instructing an AI to generate automation code.

### Question 2 🔥 **Frequently Asked**
What is **Self-Healing** in test automation? How does a self-healing algorithm prevent test script crashes when developers update element classes or IDs?

### Question 3 📌 **Important**
What is the **Model Context Protocol (MCP)**? How does MCP allow AI agents to debug failing tests on a local computer?

### Question 4 📌 **Important**
Why should you prioritize "Project Achievements with Metrics" on an automation resume rather than simply listing the tools you know? Give an example.

### Question 5 💡 **Good to Know**
How does LinkedIn's search algorithm affect how recruiters find candidates? What keywords should you include in your profile to rank for Playwright roles?

---

## Practical Assignments

### Assignment 1: Structured Prompt for Playwright POM Generation

* **Interview Relevance:** Demonstrates prompt engineering skills to automate initial code scaffolding, showcasing modern productivity tools.
* **Difficulty Level:** Easy-Intermediate
* **Concepts Covered:** Prompt structuring, role prompting, context definition, constraints.

#### Problem Statement
Write a detailed, structured prompt that instructs ChatGPT or Claude to generate a complete Page Object class and a matching Playwright test script for a student registration form.

**Form Requirements:**
- Input field: Student Name
- Select box: Branch (CS, IT, Mechanical)
- Checkbox: Hosteller (Yes/No)
- Button: Submit Registration
- Expected result: Verify success alert displays after submission.

#### Requirements
1. Structure the prompt into sections:
   - **Role:** (Assign automation role context).
   - **Context:** (Detail the form elements).
   - **Task:** (Detail what class and test script to generate).
   - **Constraints:** (Specify naming convention, no assertions in POM, use TypeScript).
2. Save this prompt inside a text block.

---

### Assignment 2: Mocking a Self-Healing Locator Function

* **Interview Relevance:** Implementing custom self-healing routines teaches core locator retry logic and exceptions handling inside test frameworks.
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** Array iteration, try-catch loops, element visibility waits, custom error handling.

#### Problem Statement
Write a helper function in TypeScript named `clickSelfHealing(page, selectors)` that attempts to locate and click an element using an array of backup selectors. If the primary selector fails (throws a timeout error), the function must catch the error, log a warning, try the next selector in the array, and click it.

#### Requirements
1. Implement the function:
   ```typescript
   async function clickSelfHealing(page: Page, selectors: string[]): Promise<void>
   ```
2. Loop through the `selectors` array.
3. Place a `try-catch` block inside the loop.
4. Attempt to wait for and click the element using the current selector with a short timeout (e.g. 2000ms).
5. If successful, break the loop and return.
6. If all selectors fail, throw a final error: `"Self-healing failed: No matching element found."`

---

### Assignment 3: Designing a GitHub Portfolio README

* **Interview Relevance:** A clean, professional README is the face of your GitHub portfolio. It is what recruiters and hiring managers look at first.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Markdown layout, folder tree structures, environment run instructions.

#### Problem Statement
Write a professional `README.md` template for a Playwright automation framework portfolio project. The template must explain the framework architecture, folder structure, setup commands, and pipeline configurations clearly.

#### Requirements
1. Create a markdown template containing:
   - **Project Title:** (e.g. *VJTI Enterprise Portal Automation Framework*).
   - **Features List:** (Page Object Model, Dynamic API Chaining, Docker runs, GHA pipelines).
   - **Directory Tree Layout:** (Show where tests, pages, and configs reside).
   - **Prerequisites:** (Node version, Docker installation).
   - **Setup Instructions:** (Clone, install dependencies, run test CLI commands).
   - **CI/CD Integration:** (Briefly explain how tests trigger in GitHub Actions).
