# Day 3: Async JavaScript and TypeScript – Trainer Guide

This guide is designed for technical trainers conducting the Day 3 live session of the Playwright Automation Bootcamp.

---

## Session Opening

### Welcome Script
> "Hello everyone, and welcome to Day 3 of the bootcamp! Today is a major milestone in our learning journey. We are stepping away from simple synchronous code to study **Asynchronous JavaScript** — how code waits for network events without locking the system. Then, we are going to upgrade our coding language to **TypeScript**, adding type-safety gates that catch bugs before execution. We'll also cover debugging techniques and the SOLID design principles. These are essential skills needed to write structured, professional automation frameworks."

### Session Goal
By the end of this session, students will be able to handle async API transactions, validate models using TypeScript Interfaces, write custom Enums, use generics, and structure classes complying with SOLID principles.

### Motivation
Draw on the whiteboard:
* A timeline showing: `Action A (Click Button)` ──► `Action B (Wait for Server Response - 5 seconds)` ──► `Action C (Assert Success)`.
* Explain: *"If JavaScript was strictly synchronous, the entire browser would freeze and become completely unresponsive for those 5 seconds. Asynchronous JavaScript allows the system to perform other tasks while waiting. Playwright depends on this to handle loading elements without lagging. Let's study how this works."*

---

## Promises

### Trainer Introduction
> "Let's look at Promises. A Promise is simply an object representing a future value that isn't ready yet. *Future me response dene ka promise karne wala object.* Think of it like booking a catering token at a college technical festival. You pay the fee, get a token (Pending), and go to the counter. If food is available, you get the plate (Resolved/Fulfilled). If the food runs out, the token is canceled (Rejected). Promises have exactly these three states: Pending, Resolved, and Rejected."

### Student Engagement Questions
* "What are the three states a Promise can be in?"
* "What method do we call to handle a Promise failure?" (catch)

### Whiteboard Teaching
Draw the catering token diagram:
* Token Issued ──► **Pending**
* Student gets food ──► resolve() ──► **Resolved** (`.then()`)
* Food runs out ──► reject() ──► **Rejected** (`.catch()`)

### Live Coding Demonstration
* **Step 1:** Write a promise mock function `const checkStatus = roll => new Promise((res, rej) => ...)`
* **Step 2:** Log output inside `.then` and `.catch` blocks.
* **Step 3:** Show how `.finally()` executes cleanups at the end.

---

## Async/Await

### Trainer Introduction
> "Writing long chains of `.then()` and `.catch()` can make code hard to read. ES8 introduced Async/Await. *Promises ke sath kaam karne ko synchronous code jaisa aasan aur clear banane ka syntax.* It is a cleaner wrapper. The `async` keyword makes a function return a promise, and `await` tells the engine to pause and wait for the promise to resolve before moving to the next line."

### Student Engagement Questions
* "Can we use `await` inside a normal, non-async function?" (No, it throws a syntax error)
* "Think of an auto-rickshaw queue. How is awaiting a passenger like `await`?"

### Whiteboard Teaching
Show side-by-side:
* Chained: `fetch().then(res => res.json()).then(data => console.log(data))`
* Async/Await: `const res = await fetch(); const data = await res.json(); console.log(data);`
Point out how much more readable the await version is.

### Live Coding Demonstration
* **Step 1:** Write `async function processFee(name, txnId) { ... }`
* **Step 2:** Call an async simulation helper `await verifyUpiPayment(txnId)`.
* **Step 3:** Show what happens if you forget to write the `await` keyword (logs a pending promise).

---

## Error Handling

### Trainer Introduction
> "As QAs, we expect failures. Error handling ensures that when a payment gateway crashes or a network times out, our test scripts don't just crash blindly. *Code crash hone se bachane ke liye try-catch block use karke runtime exceptions ko handle karna.* We wrap risky actions in a `try` block, capture exceptions in `catch`, and run final cleanup (like closing browsers) inside `finally`."

### Student Engagement Questions
* "What happens to the execution flow if an error is thrown inside a `try` block?"
* "Why should you never leave a `catch` block empty?"

### Whiteboard Teaching
Draw a railway track routing:
* Normal Track (Try block) ────► Train moves forward.
* Rock on track (Error) ──(Switch Track)──► Catch block handles redirection.
* Station arrival ────► Finally block (runs regardless of route).

### Live Coding Demonstration
* **Step 1:** Write a block parsing invalid JSON: `JSON.parse("{ bad json ")` inside `try-catch`.
* **Step 2:** Log error details in `catch(err)`.
* **Step 3:** Show resource cleanup in `finally`.

---

## Modular Programming (Import / Export)

### Trainer Introduction
> "As automation frameworks grow, keeping all code in one file is impossible. Modular programming lets us split code into separate, focused files. *Ek file ke functions ya classes ko dusri file me reuse karne ke liye export aur import karne ka tarika.* We use `export` to share classes or functions and `import` to use them in other modules."

### Student Engagement Questions
* "What is the difference between a default export and a named export?"
* "When importing a named export, do we need curly braces `{}`?" (Yes)

### Whiteboard Teaching
Draw a diagram representing a canteen:
* Canteen file: exports `itemPrice` constant, exports default class `FoodDelivery`.
* Student file: imports `FoodDelivery` and `{ itemPrice }` to run orders.

### Live Coding Demonstration
* **Step 1:** Create `canteenStall.js` exporting elements.
* **Step 2:** Create `studentPortal.js` importing them.
* **Step 3:** Explain resolving paths using relative symbols `./`.

---

## Why TypeScript & Types

### Trainer Introduction
> "JavaScript is dynamically typed. This means a variable containing a number can suddenly be reassigned to a string, causing silent runtime bugs. TypeScript is a strongly-typed language built on top of JS. *JavaScript me bugs ko run-time ke bajay compile-time par hi types ki help se catch karne ka benefit.* It catches errors during compilation before code ever runs."

### Student Engagement Questions
* "Have you ever passed a string argument to a function that expected a number, causing a NaN bug? How does TS prevent this?"

### Whiteboard Teaching
Draw the quality check checkpoint:
* JS: Assembly Line ──► Highway (crashes if a wheel is missing).
* TS: Assembly Line ──► Quality Check Checkpoint (blocks exit if a wheel is missing).

### Live Coding Demonstration
* **Step 1:** Show standard JS failing silently when adding a string to a number.
* **Step 2:** Write TS code declaring `let rollNo: number = 1005;` and show compilation error when assigning `"CS-1005"`.

---

## Interfaces, Enums, & Generics

### Trainer Introduction
> "To model complex data (like student profiles or transaction responses), we use Interfaces, Enums, and Generics.
> * **Interfaces:** Blueprints defining object schemas. *Object ke structure ko design karne ka blueprint.*
> * **Enums:** Collections of fixed choices (like CS, IT, ME departments) that prevent spelling mistakes. *Named constants ka ek collection jo limited options ko track karta hai.*
> * **Generics:** Templates (`<T>`) used to build reusable code that works with dynamic types. *Reusable syntax jo dynamic types ko respect karta hai.*"

### Student Engagement Questions
* "What is the difference between a type alias and an interface?"
* "How do enums help in writing clean config parameters?"

### Whiteboard Teaching
Draw an IRCTC train birth selector (Lower, Middle, Upper) mapping to an Enum. Draw a generic box `<T>` representing an API response carrier that wraps different details inside a type-safe `data` field.

### Live Coding Demonstration
* **Step 1:** Define interface `Student` and Enum `Department`.
* **Step 2:** Show how `ApiResponse<T>` wraps student profiles dynamically.
* **Step 3:** Show read-only errors using the `Readonly<Student>` utility type.

---

## SOLID Principles (Introduction)

### Trainer Introduction
> "As frameworks scale, they can become tangled mess structures (spaghetti code). SOLID principles are 5 architectural rules to keep code modular and readable. *Readable, maintainable, aur reusable software design ke paanch solid rules.* Today we focus on the Single Responsibility Principle: a class should do only one job. For example, Page Objects handle elements, while test suites handle assertions."

### Student Engagement Questions
* "If a class handles database writes, sends emails, and calculates fees, how many responsibilities does it have?"
* "Why is this risky?"

### Whiteboard Teaching
Draw a monolithic class doing everything vs. three small, clean, specialized classes delegating tasks via dependency interfaces.

### Live Coding Demonstration
* **Step 1:** Show a messy legacy code violating SRP (combining payment logic with logging).
* **Step 2:** Refactor it by creating an isolated `AuditLogger` and injecting a generic `PaymentMethod` interface (Paytm UPI & NetBanking) complying with SRP and OCP.

---

## Session Closing

### Session Summary
* Promises represent future states (Pending, Resolved, Rejected).
* Async/Await simplifies asynchronous execution.
* Try-Catch-Finally prevents runtime crashes.
* TypeScript adds static check gates using Types, Interfaces, Enums, and Generics.
* SOLID principles promote clean modular architecture.

### Knowledge Check Questions
1. What value is returned by an `async` function by default? (A Promise)
2. Difference between `as` type assertion and type casting?
3. What is the benefit of using a generic `<T>` model in API handlers?
4. How does the Single Responsibility Principle apply to Playwright Page Objects?
5. What does `Partial<Student>` do?

### Assignment Introduction
* **Assignment 1:** Fetch Student Enrollment Data (sequential async loop).
* **Assignment 2:** VJTI Student ERP Type System (TS structures, Partial/Readonly types).
* **Assignment 3:** SOLID Billing Transaction refactoring (SRP/OCP gateways).

### Homework Guidance
Instruct students to set up their TypeScript compilation environments (`tsc`), complete the exercises, and verify logs.

### Next Session Preview
In the next session (Day 4), we will cover version control using Git and GitHub workflows.
