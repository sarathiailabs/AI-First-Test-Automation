# Day 3: Async JavaScript and TypeScript – Student Revision Notes

This revision document is designed to help you quickly review the key concepts taught during the Day 3 lecture session.

---

## Promises

### Definition
A Promise is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value. *(Future me response dene ka promise karne wala object, jo pending, resolved, ya rejected state me hota hai.)*

### Important Syntax
```javascript
const lookupPromise = new Promise((resolve, reject) => {
    // resolve(data) on success, reject(error) on fail
});
```

### Example
```javascript
const checkStatus = (rollNo) => {
    return new Promise((resolve, reject) => {
        if (rollNo >= 1001 && rollNo <= 9999) {
            resolve("Student fee verification matches.");
        } else {
            reject("Invalid roll number.");
        }
    });
};
```

### Key Points
* Three states: Pending, Resolved (Fulfilled), and Rejected.
* Use `.then()` to capture success, `.catch()` to capture errors, and `.finally()` for cleanups.

### Common Mistakes
* **Leaving promises unhandled:** Forgetting to append `.catch()` to capture potential rejections, leading to runtime console crash errors.

---

## Async/Await

### Definition
A syntactical wrapper around promises that allows writing asynchronous code in a linear, synchronous-looking structure. *(Promises ke sath kaam karne ko synchronous code jaisa aasan aur clear banane ka syntax.)*

### Important Syntax
```javascript
async function execute() {
    const data = await getPromise();
}
```

### Example
```javascript
async function loadPortal(rollNo) {
    try {
        const message = await checkStatus(rollNo);
        console.log(message);
    } catch (err) {
        console.log("Error details:", err);
    }
}
```

### Key Points
* `async` functions automatically return a Promise.
* `await` pauses execution until the promise resolves/rejects. It can only be used inside `async` functions.

### Common Mistakes
* **Forgetting the `await` keyword:** Calling an async function without `await` stores the raw Promise object in a pending state, rather than retrieving the actual resolved data value.

---

## Error Handling

### Definition
The process of intercepting and managing runtime exceptions to prevent program crashes using `try`, `catch`, and `finally` blocks. *(Code crash hone se bachane ke liye try-catch block use karke runtime exceptions ko handle karna.)*

### Important Syntax
```javascript
try {
    // Risky operations
} catch (error) {
    // Catch crash details
} finally {
    // Cleanup executes always
}
```

### Example
```javascript
try {
    const parsedData = JSON.parse("{ invalid json }");
} catch (err) {
    console.log("Parsing failed:", err.message);
}
```

### Key Points
* If code inside `try` fails, control transfers immediately to `catch`.
* The `finally` block runs regardless of success or failure.

### Common Mistakes
* **Silent failures:** Leaving `catch` blocks empty without logs, masking critical bugs from developers.

---

## Modular Programming (Import / Export)

### Definition
Splitting code bases into separate files (modules) and sharing elements using `import` and `export` statements. *(Ek file ke functions ya classes ko dusri file me reuse karne ke liye export aur import karne ka tarika.)*

### Important Syntax
```javascript
export const item = "Vada Pav";      // Named Export
export default class FoodDelivery {}  // Default Export

import FoodDelivery, { item } from "./canteenStall.js";
```

### Key Points
* Named exports require curly braces `{}` and exact names.
* Default exports are imported without curly braces and can be renamed.

### Common Mistakes
* **Mixing up default and named exports:** Attempting to import named exports without curly braces `{}` throws import resolution errors.

---

## Debugging Techniques

### Definition
The systematic workflow of identifying, isolating, and resolving execution bugs. *(Code me errors/bugs ko dhoondhne aur fix karne ke methods (like console.log, debugger, breakpoints).)*

### Important Syntax
```javascript
console.log("Value:", variable);
debugger; // Pauses execution (if DevTools is open)
```

### Key Points
* Step through code execution line-by-line using Chrome DevTools breakpoints.
* Remove temporary debugging logs before staging commits.

---

## Why TypeScript

### Definition
A strongly-typed superset of JavaScript that adds static type checking to catch errors during compilation. *(JavaScript me bugs ko run-time ke bajay compile-time par hi types ki help se catch karne ka benefit.)*

### Example
```typescript
let rollNo: number = 1005;
// rollNo = "CS-1005"; // TypeScript blocks compilation immediately
```

### Key Points
* Prevents data mismatch bugs before execution runs.
* Restricts variables to structured, defined typings.

---

## Types and Interfaces

### Definition
* **Types:** Explicit declarations that define the classification of data a variable or custom alias can hold. *(Variables me kis type ka data store hoga, jaise string, number, etc., use manually specify karna.)*
* **Interfaces:** Syntactical contracts defining the structure models of objects, supporting inheritance extensions. *(Kisi object ke structure ya model ko design karne ke liye type validation blueprint.)*

### Important Syntax
```typescript
type StatusCode = "SUCCESS" | "FAILED"; // Type Union alias

interface Student {
    name: string;
    rollNo: number;
}
```

### Example
```typescript
interface SportsStudent extends Student {
    sportName: string;
}
```

### Key Points
* Use `type` for union types, tuples, or aliases.
* Use `interface` for modeling object structures and page models.

### Common Mistakes
* **Using `any` typing:** Defeats the compiler check entirely, rendering TypeScript's benefits useless.

---

## Enums

### Definition
A collection of named numeric or string constants used to track fixed selections. *(Named constants ka ek collection jo limited options ko track karne me help karta hai.)*

### Important Syntax
```typescript
enum Department {
    CS = "ComputerScience",
    IT = "InformationTechnology"
}
```

### Key Points
* Restricts selections to a predefined list of constants, preventing spelling typos.
* String enums log descriptive labels, making them easier to debug than numeric enums.

---

## Generics

### Definition
Parameter blueprints allowing classes, interfaces, or functions to work with various data types while maintaining type safety. *(Reusable classes aur functions banane ka syntax jo inputs ke dynamic types ko respect karte hain.)*

### Important Syntax
```typescript
interface ApiResponse<T> {
    status: number;
    data: T;
}
```

### Key Points
* Uses placeholders like `<T>` resolved at instantiation time.
* Encourages writing reusable, type-safe API client functions.

---

## Type Assertions

### Definition
A mechanism telling the TypeScript compiler to treat a variable as a specific target type, overriding default inference. *(TypeScript compiler ko batana ki hume code ke runtime type ke baare me compiler se zyada pta hai (using `as` syntax).)*

### Important Syntax
```typescript
const element = page.locator("a#home") as HTMLAnchorElement;
```

### Key Points
* Compile-time feature only; does not perform runtime conversions or casting.

---

## Utility Types

### Definition
Built-in mapping tools used to customize and transform existing type structures. *(Pehle se bane types ko customize karne ke built-in classes, jaise Partial ya Readonly.)*

### Important Syntax
```typescript
const updateProfile: Partial<Student> = { name: "Rahul" }; // All fields optional
const lockedRecord: Readonly<Student> = { name: "Priya", rollNo: 1002 }; // Immutable
```

### Key Points
* `Partial<T>` turns all properties optional.
* `Readonly<T>` blocks compilation updates on object keys.

---

## SOLID Principles (Introduction)

### Definition
Five design principles aimed at making software structures more readable, modular, and maintainable. *(Readable, maintainable, aur reusable software design ke paanch solid rules.)*

### Key Points
* **Single Responsibility Principle (SRP):** A class should do one job (e.g. Page Objects navigate elements, tests assert outcomes).
* **Open-Closed Principle (OCP):** Extend functionality by creating child classes, not modifying original code.

---

## Assignment Summary

* **Fetch Student Enrollment Data (Async/Await)**
  * *Concepts Practiced:* Asynchronous Promise chains, sequential `for...of` loops, and error recovery logs using `try-catch-finally`.
* **VJTI Student ERP Type System (TypeScript)**
  * *Concepts Practiced:* Defining Department Enums, object schemas via interfaces, type assertion overrides, and utility types (`Partial`, `Readonly`).
* **SOLID Banking Transaction System**
  * *Concepts Practiced:* Refactoring legacies to follow SRP (isolating Audit Loggers) and OCP (injecting PaymentMethod interfaces like Paytm UPI).

---

## Quick Revision Sheet

* **Promises & Async/Await:**
  ```javascript
  const resolveTask = async () => await queryDb();
  ```
* **Enums & Interfaces:**
  ```typescript
  enum Env { Prod = "PROD", Dev = "DEV" }
  interface Config { url: string; env: Env; }
  ```
* **Utility Type Mapping:**
  ```typescript
  const patch: Partial<Student> = { rollNo: 1005 };
  ```
