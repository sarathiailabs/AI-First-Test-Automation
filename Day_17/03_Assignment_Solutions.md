# Day 17: AI for Test Automation + Career Preparation – Solutions

This file contains solutions for the theory questions and practical programming assignments assigned for Day 17.

---

## Theory Question Solutions

### Question 1
What is **Prompt Engineering**? Describe the role of "Constraints" and "Role Prompting" when instructing an AI to generate automation code.

### Answer
* **Prompt Engineering:** The process of designing, structuring, and refining input queries to guide generative AI models (like ChatGPT) to produce precise and accurate code or documentation outputs.
* **Role Prompting:** Instructs the AI to adopt a specific persona (e.g. "Act as a Lead QA Automation Architect"). This forces the model to use industry best practices, clean design patterns, and accurate frameworks instead of generic scripting.
* **Constraints:** Define strict rules for what the AI *must not* do (e.g., "Do not use raw XPath locators, use page.getByRole() only" or "Do not add assertions inside Page Object files"). This ensures the output integrates directly into your existing framework structure.

---

### Question 2
What is **Self-Healing** in test automation? How does a self-healing algorithm prevent test script crashes when developers update element classes or IDs?

### Answer
* **Self-Healing:** A technique that dynamically resolves locator failures during test execution.
* **How it works:** When a test script fails to find a primary selector (like `#login-submit`), instead of throwing a timeout crash immediately, the runner consults a backup selector list or calls an AI node. It tries matching attributes like text content, parent positions, or tags. If it finds a match, it clicks the element, logs the healed selector, and completes the step, saving the pipeline from failing.

---

### Question 3
What is the **Model Context Protocol (MCP)**? How does MCP allow AI agents to debug failing tests on a local computer?

### Answer
* **MCP:** An open standard protocol that connects Large Language Model apps securely to local workstation systems (like the terminal, file editors, and run commands).
* **How it allows debugging:** Instead of a developer copying compiler errors from their screen and pasting them into ChatGPT, an MCP-enabled AI agent can run `npx playwright test` directly inside the local console, capture the failure logs, open the failing test file, write the corrected code block, and rerun the test to verify it passes—operating autonomously.

---

### Question 4
Why should you prioritize "Project Achievements with Metrics" on an automation resume rather than simply listing the tools you know? Give an example.

### Answer
* **Why prioritize:** Recruiters scan hundreds of resumes that look identical (listing "Selenium, Playwright, Java"). Highlighting metrics proves you brought value, optimized builds, and understand how to solve business problems.
* **Example:**
  * *Bad:* "Responsible for writing automation scripts using Playwright and running pipelines."
  * *Good:* "Architected a Playwright POM framework in TypeScript, reducing pipeline test execution runtime by 45% using concurrent matrix sharding in GitHub Actions."

---

### Question 5
How does LinkedIn's search algorithm affect how recruiters find candidates? What keywords should you include in your profile to rank for Playwright roles?

### Answer
* **LinkedIn Search:** Recruiters use LinkedIn Recruiter tool with specific search strings (e.g., "Playwright" AND "TypeScript" AND "CI/CD"). If your profile lacks these exact keywords in your Headline, About, and Skills sections, you will not appear in their search results list.
* **Keywords to include:** Playwright, TypeScript, JavaScript, Page Object Model (POM), CI/CD, GitHub Actions, Docker, API Automation, Framework Design.

---

## Programming Assignment Solutions

### Assignment 1: Structured Prompt for Playwright POM Generation

#### Solution (Prompt Text Block)
```text
Act as a Senior QA Automation Engineer specializing in Playwright and TypeScript.

Context: 
I need to automate a student registration page. The HTML markup of the form is:
<form id="student-reg-form">
  <input type="text" id="student-name" placeholder="Enter full name" />
  <select id="branch-select">
    <option value="cs">Computer Science</option>
    <option value="it">Information Technology</option>
  </select>
  <input type="checkbox" id="hosteller-check" />
  <button type="submit" class="submit-form-btn">Submit Registration</button>
</form>
<div class="success-toast" style="display: none;">Registration Successful!</div>

Task:
Generate two files:
1. A Page Object class named 'RegistrationPage.ts' representing this form.
2. A Playwright test file named 'registration.spec.ts' that imports the page object, fills the form fields with mock inputs (Name: "Amit Verma", Branch: "it", Hosteller: true), submits the form, and asserts that the success toast displays text "Registration Successful!".

Constraints:
- Use TypeScript with clean types.
- Declare all element locators inside the POM class constructor.
- Do not write any expect() assertions inside the Page Object class; keep them strictly in the test file.
- Use Playwright's user-facing selectors (e.g., page.getByPlaceholder) where applicable.
```

---

### Assignment 2: Mocking a Self-Healing Locator Function

#### Solution Code (`self-healing.ts`)
```typescript
import { Page } from '@playwright/test';

/**
 * Attempts to click an element by trying a list of backup selectors if the primary one fails.
 * @param page Playwright Page context
 * @param selectors Array of selectors (primary first, followed by backups)
 */
export async function clickSelfHealing(page: Page, selectors: string[]): Promise<void> {
  for (let i = 0; i < selectors.length; i++) {
    const currentSelector = selectors[i];
    console.log(`[Healing Check]: Trying locator [${currentSelector}]...`);
    
    try {
      // 1. Locate element
      const element = page.locator(currentSelector);
      
      // 2. Wait for element to become visible (short timeout of 2 seconds)
      await element.waitFor({ state: 'visible', timeout: 2000 });
      
      // 3. Click element
      await element.click();
      
      // 4. Log success statement
      if (i > 0) {
        console.warn(`[HEALED SUCCESS]: Primary selector failed. Healed dynamically using backup [${currentSelector}]`);
      } else {
        console.log(`[SUCCESS]: Located and clicked primary element.`);
      }
      return; // Exit function successfully
      
    } catch (error) {
      console.warn(`[HEALING WARN]: Locator [${currentSelector}] failed to render: ${error.message}`);
      // Continue to next selector in the loop
    }
  }
  
  // Throw final error if all selectors failed
  throw new Error("Self-healing failed: None of the provided selectors matched visible elements.");
}
```

---

### Assignment 3: Designing a GitHub Portfolio README

#### Solution (README Template Markdown)
```markdown
# VJTI Student Portal Automation Framework

A production-grade end-to-end test automation framework for the VJTI Student Portal, built using **Playwright**, **TypeScript**, **Docker**, and **GitHub Actions**.

## 🚀 Features

- **Page Object Model (POM):** Decoupled UI selector structure for clean maintenance.
- **Component Model:** Nested reusable navbar and footer widgets.
- **Shared Authentication:** Authenticates once via API and shares storage state cookies across runs to skip login screens.
- **CI/CD Integration:** Automatically triggers test matrices on GitHub push events.
- **Dockerization:** Complete container recipes to execute tests in isolated browser environments.

## 📁 Directory Structure

```text
├── .github/workflows/
│   └── playwright.yml         # GitHub Actions CI pipeline config
├── tests/
│   ├── auth.setup.ts          # Setup project saving storage state
│   ├── login.spec.ts          # Authentication test suite
│   └── grades.spec.ts         # Student grades test suite
├── pages/
│   ├── BasePage.ts            # Parent base page
│   ├── LoginPage.ts           # Login Page POM class
│   └── DashboardPage.ts       # Dashboard POM nesting Navbar component
├── Dockerfile                 # Custom Docker image build script
├── docker-compose.yml         # Container orchestration config
└── playwright.config.ts       # Global Playwright configurations
```

## 🛠️ Prerequisites

- NodeJS: `v18.x` or higher
- Docker Desktop: Installed and running (for containerized runs)

## 💻 Local Setup Instructions

1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/vjti-automation-framework.git
   cd vjti-automation-framework
   ```
2. **Install project dependencies:**
   ```bash
   npm ci
   ```
3. **Install Playwright browsers:**
   ```bash
   npx playwright install --with-deps
   ```
4. **Execute all tests:**
   ```bash
   npx playwright test
   ```

## 🐳 Running inside Docker

Run the entire automation suite inside isolated container environments without installing node locally:
```bash
docker compose up --build
```
After execution completes, test reports will be saved locally inside the `./playwright-report` folder.
```
