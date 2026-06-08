# Day 17: AI for Test Automation + Career Preparation – Trainer Guide

This Delivery Guide helps instructors teach Day 17: AI for Test Automation + Career Preparation. Follow the lesson sequences, scripts, and engagement questions below.

---

## Session Opening

### Welcome Script
> *"Good evening, class! Welcome to Day 17. Today is a special session divided into two halves. In the first half, we will learn how to use Artificial Intelligence (AI) to generate test scenarios, build prompts for locator lookups, set up self-healing test systems, and discuss Playwright MCP agents. In the second half, we will learn how to package all these skills into an interview-clearing Resume, optimize LinkedIn profiles to rank in recruiter searches, and design a professional GitHub portfolio."*

### Session Goal
Help students leverage AI productivity tools in testing and prepare their job application profiles (resumes, LinkedIn, GitHub portfolios) to secure automation roles.

### Motivation
> *"There is a famous saying in the tech industry: 'AI will not replace QA engineers. But QA engineers who use AI will replace QA engineers who don't.' AI is your partner. It takes over the typing of basic code so you can focus on framework design. Today, we learn both how to use these tools and how to get noticed by recruiters to land a dream QA role!"*

---

## Topic 1: AI in Software Testing & Prompt Engineering

### Trainer Introduction
> *"Prompt Engineering is like giving directions to an auto-rickshaw driver in Nagpur or Coimbatore. If you say: 'Take me to the station,' the driver might take a longer route. But if you say: 'Take me to Nagpur Station via the Flyover, turn left at the bridge, and avoid the market road,' you get there quickly. Prompt Engineering is giving the LLM precise instructions (Role, Context, Task, Constraints) to get exact code output."*

### Student Engagement Questions
1. *"What happens if we ask an AI to write a test without giving it the HTML markup?"*
2. *"What is a Constraint? Why is it useful to tell the AI what NOT to do?"*

### Whiteboard Teaching
Draw the Prompt Matrix structure:
```text
  +-------------------------------------------------------------+
  |                   [ THE PROMPT CONTAINER ]                  |
  |  - ROLE: Playwright Automation Specialist                   |
  |  - CONTEXT: VJTI portal HTML snippet                        |
  |  - TASK: Write a POM LoginPage class                        |
  |  - CONSTRAINTS: Use page.getByPlaceholder, no XPaths        |
  +-------------------------------------------------------------+
```

### Topic Recap
Prompt Engineering guides LLM outputs using structured rules and role settings.

### Transition Script
> *"Now let's look at how we can use these prompts to generate manual test scenarios and test datasets."*

---

## Topic 2: ChatGPT for Test Cases & Test Data Generation

### Trainer Introduction
> *"Instead of spending hours writing standard manual test inputs, we feed requirement specs to ChatGPT and instruct it to cover positive scenarios, negative inputs, and edge-cases (like text length limits and empty fields). We can also generate mock JSON data sheets automatically."*

### Topic Recap
AI automates the creation of manual test cases and mock JSON datasets based on specifications.

### Transition Script
> *"We know how to generate code. How do we let AI run tests and edit files directly on our laptops? Let's study Playwright MCP Concepts."*

---

## Topic 3: Playwright MCP Concepts

### Trainer Introduction
> *"MCP stands for Model Context Protocol. Think of it as a remote control interface. If you guide a drone using a remote control, you don't climb the hill yourself; the drone goes there. MCP is that remote control bridge. It lets the AI agent run terminal commands (like npx playwright test) and edit local files on your machine to fix bugs autonomously."*

### Topic Recap
MCP provides a secure tool-use bridge connecting LLMs to local terminals and file managers.

### Transition Script
> *"What if a selector breaks during a run and there is no AI agent? Can our framework fix itself dynamically? Yes, using Self-Healing."*

---

## Topic 4: Self-Healing Concepts

### Trainer Introduction
> *"Self-Healing is like GPS auto-rerouting. If a road is blocked (a button's ID changed), the GPS doesn't stop the car; it calculates a detour. Self-healing frameworks try backup selectors (like matching text contents or tags) when the primary selector fails, preventing test runs from crashing."*

### Student Engagement Questions
1. *"If a button class changes from '.btn-primary' to '.btn-submit', how can a self-healing tool find it? (Answer: By checking backup tags like button text content)."*

### Topic Recap
Self-healing resolves locator updates at runtime using weighted backup selector arrays.

### Transition Script
> *"Now we understand the modern AI automation trends. In the second half, let's learn how to showcase these skills to land a job. Let's start with Resume Building."*

---

## Topic 5: Resume Building

### Trainer Introduction
> *"A resume is your shop billboard. If a billboard says 'We sell groceries', it's boring. If it says 'Get fresh organic mangoes, delivery in 10 minutes', customers flock. Your resume shouldn't just list 'Playwright, Git'. It must show metrics: 'Architected a Playwright framework, reduced pipeline runs by 40% using sharding'. Always start project descriptions with strong action verbs."*

### Student Engagement Questions
1. *"Which description sounds better to an interviewer: 'I know how to write scripts in Playwright' or 'Built a Page Object Model framework and integrated it with GitHub Actions pipelines'?"*

### Topic Recap
Resumes should emphasize project architectures, frameworks metrics, and automation achievements.

### Transition Script
> *"Your resume is ready. How do we make sure recruiters find you online? Let's optimize LinkedIn profiles."*

---

## Topic 6: LinkedIn Optimization

### Trainer Introduction
> *"Recruiters search LinkedIn using keyword queries like 'Playwright AND TypeScript AND CI/CD'. If these keywords don't exist in your Headline or Experience sections, you are invisible to their search engines. Optimize your headline to show your core tools and role target clearly."*

### Topic Recap
LinkedIn optimization uses keyword targeting in headlines and summaries to rank in recruiter searches.

### Transition Script
> *"Ranking in search gets recruiters to visit your profile. What should they see when they visit? A public GitHub Portfolio."*

---

## Topic 7: Portfolio Preparation & GitHub Best Practices

### Trainer Introduction
> *"Your GitHub profile is your portfolio. Recruiters scan your repository's README page first. A clean README must show a directory tree structure, local setup instructions, and compose run commands. Make sure to commit changes using clear descriptions (`feat: add...`) and use feature branches."*

### Student Engagement Questions
1. *"Why is a repository without a README file invisible to hiring managers? (Answer: They don't have time to download and guess how your framework works; they need a clear summary page)."*

### Topic Recap
Portfolios demonstrate coding capability via documented, public GitHub repositories following version control rules.

---

## Session Closing

### Session Summary
* We learned Prompt Engineering rules (Role, Context, Task, Constraints) to guide AI code generation.
* We automated manual test case writing and mock JSON data creation.
* We explored Playwright MCP agentic coding bridges and runtime self-healing concepts.
* We formatted action-verb resumes, optimized LinkedIn keyword headers, and set up GitHub portfolios.

### Knowledge Check Questions
1. *"What are the 4 parts of a structured prompt? (Answer: Role, Context, Task, Constraints)."*
2. *"How does self-healing prevent pipeline failures? (Answer: By trying backup selector paths)."*
3. *"Where do recruiters find candidates online? (Answer: Keyword search algorithms)."*
4. *"What should be included in a portfolio README? (Answer: Folder tree, setup steps, compose run commands)."*
5. *"Why are metrics important on a resume?"*

### Homework Guidance
Instruct students to complete the `Day_17` assignments:
1. Structured Playwright POM generation prompt.
2. Custom self-healing locator helper function in TypeScript.
3. High-quality GitHub portfolio README configuration.

### Next Session Preview
In Day 18 (E-Commerce UI Automation Framework), we begin our hands-on Industry Projects. We will build a complete, production-grade Playwright framework for an e-commerce platform incorporating POM, custom fixtures, HTML reports, and pipeline runs.
