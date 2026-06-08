# Day 17: AI for Test Automation + Career Preparation – Student Revision Notes

This revision sheet provides a quick-revision summary of AI tool integrations, prompt engineering, self-healing frameworks, resume metrics, LinkedIn keywords, and GitHub portfolio setup for Day 17.

---

## Topic Revision

### 1. AI in Software Testing & Prompt Engineering
* **Definition:** Using LLMs and structured prompts to write initial test scripts, debug log errors, and generate mock details. *(AI aur detailed prompts ke help se test scripts, manual scenarios, aur data generate karna).*
* **Key Prompting Sections:**
  - **Role:** Tell AI who it is (e.g. "Act as a Playwright Expert").
  - **Context:** Give the HTML or requirements.
  - **Constraints:** Define strict rules (e.g. "Do not use XPath").

---

### 2. ChatGPT for Test Cases & Data
* **Definition:** Reading requirement specs to generate test scenarios and JSON/CSV datasets.
* **Key Benefit:** Accelerates manual case creation and database mock seeding.

---

### 3. Playwright MCP Concepts
* **Definition:** Model Context Protocol is an open standard allowing AI agents to trigger terminal commands (like running playwright tests) and editing files. *(AI agent ko terminal command run karne aur local files edit karne ka permission bridge).*

---

### 4. Self-Healing Concepts
* **Definition:** An automation execution setup where a failing locator is dynamically resolved by running alternative backup locators instead of crashing. *(Locator fail hone par script automatically backup selectors search karti hai crash ko rokne ke liye).*

---

### 5. Resume Building & LinkedIn Optimization
* **Definition:** Optimizing profiles and resumes using framework metrics and target keywords (e.g., Playwright, TypeScript, CI/CD, POM).
* **Key resume point formula:** Start with an action verb, describe the framework architecture, and end with a measurable metric (e.g., "Reduced run times by 45% using matrix sharding").

---

### 6. Portfolio Preparation & GitHub Best Practices
* **Definition:** Hosting a clean, public GitHub repository showcasing Page Objects, configs, Docker setups, and a detailed README.md file.
* **Best Practices:** Use branches for features, commit clear descriptions (`feat: add...`), and add a detailed project setup guide.

---

## Assignment Summary

During this session, we practice:
1. **POM Generation Prompt:** Write a detailed prompt to generate Playwright POM files.
2. **Self-Healing Locator Script:** Build a TypeScript function trying alternate selectors on failure.
3. **GitHub Project README:** Structure a professional markdown file detailing automation framework properties.

---

## Quick Revision Sheet

| Concept | Key Implementation | Target Area | Professional Benefit |
| --- | --- | --- | --- |
| **Prompt Eng.** | Role + Context + Task + Rules | AI script generation | Speeds up initial coding |
| **Self-Healing**| Try-catch loop on selector lists | Runtime locator crash | Prevents pipeline failures |
| **Resume Point**| Verb + Framework Project + Metric | QA Resume | Clears recruiter screeners |
| **LinkedIn** | Keywords in Headline / Experience | Recruiter searches | Boosts profile visibility |
| **Portfolio** | GitHub repo with detailed README | Code Showcase | Proves automation skill |
| **MCP** | AI Agent-to-terminal interface | Code execution | Autonomous debugging |

---

## Important Takeaways

1. **AI is a Helper:** AI tools do not replace QA analytical roles; they take over repetitive scripting tasks so you can focus on integration architecture.
2. **Showcase Framework Metrics:** Resumes that list metrics (e.g., sharding speedups, test coverage percentage) stand out over list-heavy profiles.
3. **README is Crucial:** Hiring managers scan the README page of your GitHub repository to judge framework designs in seconds.
