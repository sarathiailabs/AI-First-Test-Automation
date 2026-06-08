# Day 13: Framework Design – Trainer Guide

This handbook helps instructors deliver Day 13: Framework Design. Follow the whiteboard guidelines and interactive coding exercises to guide students.

---

## Session Opening

### Welcome Script
> *"Good evening, class! Welcome to Day 13. Up to this point, we wrote direct scripts. If we need a button click, we write `page.click()`. But what happens if you have 200 tests and the developer updates that button's CSS selector class? Your entire test suite breaks! Today, we are learning Framework Design. We will learn how to write structured, maintainable code so that locator updates take less than a minute. Let's dive in!"*

### Session Goal
Teach students how to design a modular test framework. Explain how to split selectors and action flows into Page Objects and Components, implement BasePage inheritance, create utilities, and manage failure logs.

### Motivation
> *"Imagine you build a car. If the tyres, engine, doors, and seats are welded together as one single block, replacing a punctured tyre is impossible. You have to scrap the whole car. A bad framework is like that welded car. A professional framework is built modularly, where tyres and engines are separate components that can be replaced in seconds. That's the power of Page Object Model!"*

---

## Topic 1: Framework Design Principles

### Trainer Introduction
> *"Framework Design Principles are modular organization rules. The most important rule is DRY: Don't Repeat Yourself. If you write the same locator twice, you are committing an automation crime. We separate locators, test configurations, helper utilities, and assertions into different sections so our project is easy to manage."*

### Student Engagement Questions
1. *"What happens to our automation pipeline if a developer changes the login submit button's ID from '#submit' to '#login-submit', and we have 50 test files using the old ID?"*
2. *"Can anybody name a kitchen drawer example that explains separation of concerns?"*

### Whiteboard Teaching
```text
  [ Spaghetti Framework (Bad) ]  ──► [ Modular Framework (Good) ]
  Test Scripts hold all:         ──►  - Test files: Assertions only
   - URLs, CSS selectors,        ──►  - Page Object files: Selectors/Actions
   - Fills, clicks, asserts      ──►  - Utils: Excel readers, datetime helpers
```

### Topic Recap
Principles like DRY and Single Responsibility keep code decoupled and easy to update.

### Transition Script
> *"Now that we know the theory, let's learn the most popular API pattern that implements these design goals: the Page Object Model (POM)."*

---

## Topic 2: Page Object Model (POM)

### Trainer Introduction
> *"Page Object Model (POM) maps every screen or page in your app to a class file. If you have a Login Page, you write a `LoginPage` class. The locators are defined inside the constructor, and the user actions (like filling fields and submitting) are defined as methods. The test script imports this class and calls those methods. No raw CSS selectors are allowed in the test file!"*

### Student Engagement Questions
1. *"Where should we write validations/assertions like `expect()`? Inside the LoginPage class or the test spec file?"* (Answer: Spec file).
2. *"Why is it bad to put expect() statements inside POM classes?"*

### Whiteboard Teaching
Draw the separation:
```text
  LoginPage.ts (Page Object)   ◄──►   vjti-login.spec.ts (Test Script)
  Holds: #username, #password         Instantiates page class, calls page.login()
  Action: login(user, pass)           Asserts: expect(page).toHaveURL(...)
```

### Live Coding Demonstration
#### Step 1: Type
Show page object constructor:
```typescript
class LoginPage {
  constructor(page) {
    this.page = page;
    this.username = page.locator('#username');
  }
  async login(user) {
    await this.username.fill(user);
  }
}
```

### Topic Recap
POM encapsulates page elements and action paths inside page-specific classes.

### Transition Script
> *"If POM represents entire web pages, how do we handle reusable segments like header panels that show up on *every* page? We use the Component Object Model."*

---

## Topic 3: Component Object Model

### Trainer Introduction
> *"The Component Object Model is an extension of POM. A webpage has sections like a navigation header or sidebar. Instead of declaring header locators in all Page classes, we create a separate component class, like `HeaderComponent`. We then nest this component inside our main page objects."*

### Student Engagement Questions
1. *"If we have 15 different page objects, does it make sense to define the 'Logout' locator inside every single class? How does a component class help?"*

### Whiteboard Teaching
```text
  +--------------------------------------------+
  |              [ HeaderComponent ]           |
  +--------------------------------------------+
  | [ SidebarComponent ] |   Dashboard Page    |
  +----------------------+---------------------+
```

### Topic Recap
Component model isolates reusable widgets, nesting them as object properties inside page classes.

### Transition Script
> *"To avoid writing duplicate code inside different page classes, we can also use inheritance. Let's study how Base Classes help."*

---

## Topic 4: Base Classes

### Trainer Introduction
> *"A Base Class (like `BasePage`) holds global locators and actions (like navigating to URLs or waiting for page loaders to disappear). All child Page classes inherit these actions using the `extends` keyword, saving us from writing boilerplate navigation scripts repeatedly."*

### Student Engagement Questions
1. *"What does the `super(page)` command do in JavaScript classes? (Answer: Invokes the constructor of the parent class)."*

### Whiteboard Teaching
```text
            [ BasePage ]  (Holds common navigation, loaders, logo check)
                 ▲
                 │ (extends)
          [ LoginPage ]   [ DashboardPage ] (Inherits all BasePage methods)
```

### Topic Recap
Base classes bundle shared actions (like page loading and header validations) using class inheritance.

### Transition Script
> *"Base classes handle page actions. But what about tasks that don't involve the browser page at all, like calculating a date or reading a configuration? We handle those inside Utilities."*

---

## Topic 5: Utilities

### Trainer Introduction
> *"Utilities are helper classes with static functions that handle data processing tasks. They don't need a Playwright `Page` object. For example, if you need a random email to register a student, you call `StringUtility.getRandomEmail()`."*

### Live Coding Demonstration
#### Step 1: Type
```typescript
class StringUtility {
  static getRandomName() {
    return 'Student_' + Math.floor(Math.random() * 1000);
  }
}
const randomName = StringUtility.getRandomName();
console.log(randomName);
```
#### Step 3: Expected Output
```text
Student_482
```

### Topic Recap
Utilities handle browser-agnostic routines (like calculations and file parsing) using static helper files.

### Transition Script
> *"Sometimes we encounter custom visual elements, like calendar dropdowns or date selectors. How do we automate these cleanly? We use Reusable Components."*

---

## Topic 6: Reusable Components

### Trainer Introduction
> *"A Reusable Component wraps a complex HTML widget (like a calendar date picker) into a custom helper class. Instead of writing 5 clicks to select a date in your test scripts, you write a single line: `await calendar.selectDate('2026-06-08')`."*

### Whiteboard Teaching
Show wrapper structure:
```text
  Calendar Component ──► Encapsulates clicks (Input Box click -> Year -> Day cell)
```

### Topic Recap
Reusable components wrap complex widgets into custom helper classes.

### Transition Script
> *"Finally, even the best framework encounters failures. Let's look at how we capture logs and troubleshoot crashes using Error Handling."*

---

## Topic 7: Error Handling

### Trainer Introduction
> *"Error Handling means using Try-Catch blocks to prevent tests from crashing abruptly, logging detailed error traces, and taking screenshots when a step fails so developers can diagnose the issue."*

### Student Engagement Questions
1. *"Why is an empty catch block dangerous in a test script?"*

### Live Coding Demonstration
#### Step 1: Type
```typescript
try {
  await page.locator('#missing-element').click({ timeout: 2000 });
} catch (error) {
  console.error('Action Failed: Missing element: ' + error.message);
  await page.screenshot({ path: 'screenshots/failure.png' });
  throw error; // Rethrow to fail the test officially
}
```

### Topic Recap
Try-catch blocks handle exceptions, log traces, capture failure screenshots, and execute teardowns.

---

## Session Closing

### Session Summary
* We covered DRY, Single Responsibility, and Separation of Concerns principles.
* We mapped page assets using the Page Object Model (POM).
* We nested reusable components (Navbar, Header) inside pages.
* We inherited core page actions from `BasePage` and created utility functions.
* We wrapped complex widgets into custom component objects.
* We set up Try-Catch blocks to handle exceptions and generate debug screenshots.

### Knowledge Check Questions
1. *"What is the main benefit of POM? (Answer: Selector centralization)."*
2. *"Do we write expect() assertions inside POM page classes? (Answer: No, keep them in spec files)."*
3. *"How does BasePage save code? (Answer: Through inheritance of common methods)."*
4. *"Where do we place static data formatters? (Answer: Utilities)."*
5. *"Why is 'throw error' required in a catch block? (Answer: To avoid reporting a failed test as a pass)."*

### Homework Guidance
Instruct students to complete the `Day_13` assignments:
1. `VJTI_LoginPage.ts` (Login Page class)
2. `DashboardPage.ts` (Nesting navbar component)
3. Refactor direct scripts to use Page Object Model structure.

### Next Session Preview
In the next session (Day 14: Fixtures + Hooks + Test Data), we will learn about test fixtures, setup and teardown hooks, shared authentications, and reading data dynamically from external JSON files.
