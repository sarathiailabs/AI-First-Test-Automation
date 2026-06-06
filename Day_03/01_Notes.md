# Day 3: Async JavaScript and TypeScript

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Promises | 15 mins |
| Async/Await | 15 mins |
| Error Handling | 10 mins |
| Modular Programming (Import / Export) | 10 mins |
| Debugging Techniques | 10 mins |
| Why TypeScript | 10 mins |
| Types | 10 mins |
| Interfaces | 10 mins |
| Enums | 5 mins |
| Generics (Basic) | 10 mins |
| Type Assertions | 5 mins |
| Utility Types | 5 mins |
| SOLID Principles (Introduction) | 5 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Handle asynchronous operations using Promises and Async/Await.
* Catch and process runtime exceptions using structured Try-Catch blocks.
* Organize code bases into reusable files using modular import/export syntax.
* Locate and troubleshoot execution logic using browser debugger and console features.
* Justify and implement static type safety using TypeScript.
* Design robust objects and generic response models using Interfaces and Types.
* Apply basic SOLID design patterns to structure automation frameworks.

---

## Introduction

As modern web applications migrate towards real-time data streaming and complex microservices, automation scripts must manage actions that don't execute instantly. Loading web elements, sending API calls, or verifying database records are asynchronous tasks. Understanding Async JavaScript is essential to keep Playwright tests running without hitting flakey timeouts.

Furthermore, scale brings complexity. Implementing TypeScript introduces compile-time validation to catch bugs before running scripts, while SOLID principles help structure maintainable, production-ready Page Object Model frameworks.

---

## Promises

### Definition
A **Promise** is a placeholder object representing the eventual completion (or failure) of an asynchronous operation and its resulting value. *(Future me response dene ka promise karne wala object, jo pending, resolved, ya rejected state me hota hai.)*

### Key Concepts
* **Three States:**
  1. *Pending:* The initial, active state of the operation.
  2. *Fulfilled (Resolved):* The operation completed successfully.
  3. *Rejected:* The operation failed with an error.
* **Chaining:** Use `.then()` to handle successful outcomes, `.catch()` to handle failures, and `.finally()` to run clean-ups regardless of results.

### Syntax
```javascript
const myPromise = new Promise((resolve, reject) => {
    // Async work here
    if (success) {
        resolve(resultData);
    } else {
        reject(errorMsg);
    }
});
```

### Visual Explanation
Analogy: Booking catering tokens at a college festival.
```text
[ Token Issued (Pending) ]
       │
       ├─────► Token Checked & Food Served ──► [ Resolved (.then) ]
       │
       └─────► Stall Out of Food / Canceled ──► [ Rejected (.catch) ]
```

### Example
#### Code
```javascript
const fetchRollNumberStatus = (rollNo) => {
    return new Promise((resolve, reject) => {
        console.log(`Checking status for Roll No: ${rollNo} in VJTI database...`);
        setTimeout(() => {
            const isRegistered = rollNo >= 1001 && rollNo <= 9999;
            if (isRegistered) {
                resolve({ studentRoll: rollNo, feePaid: true });
            } else {
                reject(`Roll Number ${rollNo} is not registered in ERP.`);
            }
        }, 1000);
    });
};

fetchRollNumberStatus(1005)
    .then(data => console.log("Success:", data))
    .catch(err => console.log("Error:", err))
    .finally(() => console.log("Database lookup cycle complete."));
```
#### Output
```text
Checking status for Roll No: 1005 in VJTI database...
Success: { studentRoll: 1005, feePaid: true }
Database lookup cycle complete.
```
#### Explanation
1. `fetchRollNumberStatus(1005)` returns a Promise. The state is initially `Pending`.
2. Inside `setTimeout`, the check resolves the promise since `1005` is valid.
3. `.then()` receives the resolved object and prints success, and `.finally()` runs at the end.

### Real World Usage
Handling asynchronous database connections or custom waiting conditions in automation scripts:
```javascript
const elementVisible = new Promise(resolve => setTimeout(resolve, 5000));
```

### Common Mistakes
* **Promise nesting (Pyramid of Doom):** Nesting multiple `.then()` blocks inside each other instead of chaining them sequentially or using async/await.

### Topic Summary
Promises handle future values. They transition from Pending to either Resolved or Rejected.

---

## Async/Await

### Definition
**Async/Await** is a syntactical wrapper around promises that allows writing asynchronous code in a linear, synchronous-looking structure. *(Promises ke sath kaam karne ko synchronous code jaisa aasan aur clear banane ka syntax.)*

### Key Concepts
* **`async` Keyword:** Placed before a function declaration. It ensures the function always returns a Promise.
* **`await` Keyword:** Can only be used inside `async` functions. It pauses execution until the promise resolves/rejects.
* **Sequential vs Parallel:** Awaiting every line runs operations sequentially. Running promises together (using `Promise.all`) runs them in parallel.

### Syntax
```javascript
async function getReport() {
    const data = await fetchDatabaseRecord();
    return data;
}
```

### Visual Explanation
Analogy: An auto-rickshaw queue.
```text
Without await (Non-blocking): Rickshaw driver leaves immediately before passengers board.
With await: Driver pauses (waits) until the passenger boards, then moves to the destination.
```

### Example
#### Code
```javascript
const verifyUpiPayment = (transactionId) => {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve(`Payment receipt verified for ${transactionId}`);
        }, 1000);
    });
};

async function processAdmissionFee(studentName, transactionId) {
    console.log(`Processing admission for ${studentName}...`);
    const status = await verifyUpiPayment(transactionId); // Pauses here
    console.log(status);
    console.log("Admission processed successfully.");
}

processAdmissionFee("Rahul Verma", "TXN98765");
```
#### Output
```text
Processing admission for Rahul Verma...
Payment receipt verified for TXN98765
Admission processed successfully.
```
#### Explanation
1. `processAdmissionFee` is an async function.
2. When it hits `await verifyUpiPayment(transactionId)`, the engine pauses execution of this function until the timer resolves.
3. Once resolved, the message is logged and execution completes sequentially.

### Real World Usage
Playwright scripts rely almost entirely on async/await to interact with pages:
```javascript
await page.goto("https://vjti.ac.in");
await page.click("#login-btn");
```

### Common Mistakes
* **Forgetting the `await` keyword:** Calling an async function without `await` returns the Pending Promise object instead of the actual data result.

### Topic Summary
Async/Await runs asynchronous promises using a clean, synchronous-looking layout.

---

## Error Handling

### Definition
**Error Handling** is the process of intercepting and managing runtime exceptions to prevent program crashes using `try`, `catch`, and `finally` blocks. *(Code crash hone se bachane ke liye try-catch block use karke runtime exceptions ko handle karna.)*

### Key Concepts
* **`try` Block:** Code that might throw an exception is placed inside this block.
* **`catch` Block:** Executed only if an error is thrown inside `try`. Receives the error object.
* **`finally` Block:** Executed regardless of whether an error was thrown or caught. Useful for resources cleanup.

### Syntax
```javascript
try {
    // Risky code
} catch (error) {
    // Error recovery logic
} finally {
    // Cleanup code
}
```

### Example
#### Code
```javascript
function loadConfig(configJson) {
    try {
        console.log("Parsing configuration file...");
        const parsed = JSON.parse(configJson);
        console.log("Config loaded:", parsed.env);
    } catch (err) {
        console.log("[CRITICAL ERROR] Failed to parse config JSON!");
        console.log("Error details:", err.message);
    } finally {
        console.log("System initialization sequence ended.");
    }
}

// Pass invalid JSON to trigger catch
loadConfig("{ env: 'Staging' "); // Missing quotes
```
#### Output
```text
Parsing configuration file...
[CRITICAL ERROR] Failed to parse config JSON!
Error details: Unexpected token e in JSON at position 2
System initialization sequence ended.
```
#### Explanation
1. `JSON.parse` fails due to bad JSON syntax, throwing a syntax error.
2. The execution jumps immediately to the `catch` block, printing the custom warning and error details.
3. The `finally` block runs to conclude the transaction.

### Real World Usage
Recovering from network failures or verifying elements during test execution:
```javascript
try {
    await page.waitForSelector(".loading-spinner", { timeout: 2000 });
} catch (error) {
    console.log("Spinner did not load, continuing test execution.");
}
```

### Common Mistakes
* **Empty catch blocks:** Catching an error and doing nothing (silent failures) makes troubleshooting system bugs very difficult. Always log or throw the error.

### Topic Summary
`try-catch-finally` handles runtime exceptions safely, avoiding system crashes.

---

## Modular Programming (Import / Export)

### Definition
**Modular programming** is the design of splitting code into separate files (modules) and sharing variables, functions, or classes using `import` and `export` statements. *(Ek file ke functions ya classes ko dusri file me reuse karne ke liye export aur import karne ka tarika.)*

### Key Concepts
* **Named Exports:** Export multiple elements per file. Must be imported using exact matching names inside curly braces `{}`.
* **Default Exports:** Export a single primary element per file. Can be imported using any custom name without curly braces.

### Syntax
```javascript
// Exporting from maths.js
export const pi = 3.14;
export default class Calculator {}

// Importing into app.js
import Calculator, { pi } from "./maths.js";
```

### Visual Explanation
Analogy: College canteen stalls.
```text
[ Canteen Chai Stall (Module A) ] ──► exports "Chai", "Coffee"
                                            │
                                            ▼ (Imported by student)
[ Classroom (Module B) ] ──────────► imports { Chai } to drink during lecture
```

### Example
#### Code
**canteenStall.js**
```javascript
// Named Export
export const itemPrice = 120;

// Default Export
export default class FoodDelivery {
    dispatchOrder(item) {
        console.log(`Dispatched ${item} from college canteen.`);
    }
}
```
**studentPortal.js**
```javascript
import FoodDelivery, { itemPrice } from "./canteenStall.js";

const order = new FoodDelivery();
order.dispatchOrder("Samosa Plate");
console.log(`Charged Amount: ₹${itemPrice}`);
```
#### Output
```text
Dispatched Samosa Plate from college canteen.
Charged Amount: ₹120
```
#### Explanation
1. `canteenStall.js` defines an exported constant `itemPrice` and a class `FoodDelivery`.
2. `studentPortal.js` imports the class (as the default export) and the price (as a named export inside `{}`), utilizing them.

### Real World Usage
Organizing page objects and configs into separate files within automation frameworks:
```javascript
import { testConfig } from "./test.config.js";
import LoginPage from "./pages/LoginPage.js";
```

### Common Mistakes
* **Forgetting file extensions in node imports:** Omitting `.js` or `.ts` extension when using ES modules in local environments can trigger resolution errors.

### Topic Summary
Use `export` to expose features from a file and `import` to use them in other files.

---

## Debugging Techniques

### Definition
**Debugging** represents the systematic workflow of identifying, isolating, and resolving execution bugs using logs, breakpoints, or compiler inspector flags. *(Code me errors/bugs ko dhoondhne aur fix karne ke methods (like console.log, debugger, breakpoints).)*

### Key Concepts
* **Console Logging:** Printing values using `console.log()` to check variable states.
* **`debugger` Statement:** Code insertion that triggers the browser debugger to pause execution at that line.
* **Browser Inspector:** Using breakpoints in Chrome DevTools to trace call stacks and inspect scoped variables dynamically.

### Syntax
```javascript
console.log("Current value:", variable);
debugger; // Execution pauses here if devtools is open
```

### Example
#### Scenario
Finding a bug in a scholarship discount calculation.
```javascript
function getDiscountedFees(baseFee, discountCode) {
    let finalFee = baseFee;
    
    console.log(`[DEBUG] Inputs - Base Fee: ${baseFee}, Code: ${discountCode}`);
    
    if (discountCode === "STUDENT10") {
        // Bug: dividing by 100 instead of multiplying by 0.9
        finalFee = baseFee / 10; 
        console.log(`[DEBUG] Applied discount. Final: ${finalFee}`);
    }
    
    return finalFee;
}
```

### Real World Usage
Using Playwright inspector or VS Code debug extensions to step through test actions line-by-line:
```bash
npx playwright test --debug
```

### Common Mistakes
* **Leaving debug statements in production code:** Committing active `debugger` or verbose `console.log` statements slows down pipelines and clutters build records.

### Topic Summary
Locate execution bugs using console statements, active debugger pauses, and step breakpoints.

---

## Why TypeScript

### Definition
**TypeScript** is a strongly-typed programming language built as a strict superset of JavaScript, adding static type checking to catch errors during development. *(JavaScript me bugs ko run-time ke bajay compile-time par hi types ki help se catch karne ka benefit.)*

### Key Concepts
* **Static Typing:** Types are verified during compilation, preventing type mismatch errors before code execution.
* **Superset Status:** Every valid JavaScript file is a valid TypeScript file.
* **Rich IDE Support:** Enables exact autocomplete, parameter info, and code navigation.

### Visual Explanation
Analogy: Auto quality check checkpoint.
```text
JavaScript: [ Assemble Bus ] ──► [ Drive immediately on Highway ] ──► Crash if wheel is missing!
TypeScript: [ Assemble Bus ] ──► [ Quality Check Checkpoint ] ──► Fix missing wheel ──► [ Highway ]
```

### Example
#### Code (TypeScript)
```typescript
function calculateGst(billAmount: number): number {
    return billAmount * 0.18;
}

// TypeScript editor throws immediate warning:
// calculateGst("One Thousand"); // Argument of type 'string' is not assignable to 'number'
```

### Real World Usage
Writing robust Page Object Models where page methods enforce exact parameter types (like strings for user login credentials):
```typescript
async login(username: string, age: number) {}
```

### Common Mistakes
* **Using `any` everywhere:** Assigning `any` bypasses the type checker entirely, defeating the purpose of using TypeScript.

### Topic Summary
TypeScript adds compilation validation checks to prevent runtime data-type mismatches.

---

## Types

### Definition
**Types** in TypeScript are explicit declarations that define the classification of data a variable, parameter, or function can hold. *(Variables me kis type ka data store hoga, jaise string, number, etc., use manually specify karna.)*

### Key Concepts
* **Primitive Types:** `string`, `number`, `boolean`, `null`, `undefined`.
* **Array Types:** Declared using `type[]` or `Array<type>`.
* **Any & Unknown:** `any` disables checks; `unknown` requires type verification before operations.
* **Type Aliases:** Create custom type blueprints using the `type` keyword.

### Syntax
```typescript
let studentName: string = "Rahul";
let age: number = 21;
let marksList: number[] = [85, 90, 78];

type Transaction = {
    id: string;
    amount: number;
};
```

### Example
#### Code
```typescript
type StudentProfile = {
    name: string;
    rollNo: number;
    hasPaidFees: boolean;
};

const student Rahul: StudentProfile = {
    name: "Rahul Verma",
    rollNo: 1005,
    hasPaidFees: true
};

console.log(`Student: ${student.name}, Fees Paid: ${student.hasPaidFees}`);
```
#### Output
```text
Student: Rahul Verma, Fees Paid: true
```
#### Explanation
The `StudentProfile` type enforces that any matching object must contain a string `name`, a number `rollNo`, and a boolean `hasPaidFees`.

### Real World Usage
Enforcing configuration settings parameters in test scripts:
```typescript
type TestConfig = {
    url: string;
    timeout: number;
};
```

### Common Mistakes
* **Mismatching declaration types:** Assigning a string value to a variable typed as a number.

### Topic Summary
Types enforce data classifications for variables, parameters, and custom structures.

---

## Interfaces

### Definition
An **Interface** is a syntactical contract that defines the structure and property models of an object, supporting extensions via inheritance. *(Kisi object ke structure ya model ko design karne ke liye type validation blueprint.)*

### Key Concepts
* **Declaration Contract:** Enforces class or object properties.
* **Extensibility:** Multiple interfaces can extend each other using the `extends` keyword.
* **Optional Properties:** Appending a `?` after property names declares them as optional.

### Syntax
```typescript
interface Person {
    name: string;
    age?: number; // Optional
}

interface Student extends Person {
    rollNo: number;
}
```

### Example
#### Code
```typescript
interface UserProfile {
    name: string;
    email: string;
    hostelRoom?: number; // Optional property
}

interface SportsStudent extends UserProfile {
    sportName: string;
}

const athlete: SportsStudent = {
    name: "Vikram Malhotra",
    email: "vikram@vjti.ac.in",
    sportName: "Cricket" // hostelRoom omitted (optional)
};

console.log(`${athlete.name} plays ${athlete.sportName}`);
```
#### Output
```text
Vikram Malhotra plays Cricket
```
#### Explanation
1. `UserProfile` declares `hostelRoom` as optional.
2. `SportsStudent` extends `UserProfile`, meaning it inherits `name` and `email` properties.
3. The `athlete` object matches the `SportsStudent` interface requirements successfully.

### Real World Usage
Modeling API JSON responses and Page Object structures:
```typescript
interface ApiResponse {
    status: number;
    data: object;
}
```

### Common Mistakes
* **Mismatching interface properties:** Omitting mandatory properties or using wrong data types throws compilation warnings.

### Topic Summary
Interfaces define structural blueprints for objects and classes, supporting extension.

---

## Enums

### Definition
An **Enum** (Enumerated Type) is a feature that allows defining a set of named numeric or string constants to track fixed selections. *(Named constants ka ek collection jo limited options ko track karne me help karta hai.)*

### Key Concepts
* **Fixed Selections:** Restricts variables to a predefined subset of options (e.g. days of the week, transaction states).
* **Numeric vs. String:** Numeric enums auto-increment from 0; string enums require explicit string values.

### Syntax
```typescript
enum TransactionStatus {
    Pending = "PENDING",
    Success = "SUCCESS",
    Failed = "FAILED"
}
```

### Visual Explanation
Analogy: IRCTC train birth seats selection.
```text
Enum Options:
[ Lower Berth ]  ◄── (Constants defined)
[ Middle Berth ]
[ Upper Berth ]  ◄── Selection restricted to these 3 labels only
```

### Example
#### Code
```typescript
enum Department {
    ComputerScience = "CS",
    InformationTechnology = "IT",
    Mechanical = "ME"
}

interface StudentRecord {
    name: string;
    dept: Department;
}

const student1: StudentRecord = {
    name: "Priya Sharma",
    dept: Department.InformationTechnology
};

console.log(`${student1.name} belongs to ${student1.dept} department.`);
```
#### Output
```text
Priya Sharma belongs to IT department.
```
#### Explanation
`Department` enum forces the department code to map only to `"CS"`, `"IT"`, or `"ME"`, preventing spelling or lookup errors.

### Real World Usage
Restricting configuration selections (like environment names) in testing scripts:
```typescript
enum Environment {
    Staging = "STAGING",
    Production = "PROD"
}
```

### Common Mistakes
* **Using numeric enums expecting strict string labels:** If not explicitly assigned string values, numeric enums resolve to indexes `0`, `1`, `2` in logs, causing confusion.

### Topic Summary
Enums hold a set of read-only constants for clean and type-safe selections.

---

## Generics (Basic)

### Definition
**Generics** are parameter blueprints that allow creating reusable classes, interfaces, or functions that can work with a variety of data types instead of a single one. *(Reusable classes aur functions banane ka syntax jo inputs ke dynamic types ko respect karte hain.)*

### Key Concepts
* **Type Parameters:** Denoted using `<T>`, where `T` acts as a dynamic type placeholder resolved when the function/class is instantiated.
* **Flexibility:** Allows writing code that is flexible yet type-safe.

### Syntax
```typescript
function printValue<T>(value: T): T {
    return value;
}
```

### Example
#### Code
```typescript
// Generic API response model
interface ApiResponse<T> {
    statusCode: number;
    data: T; // Dynamic data payload type
}

interface HostelDetail {
    roomNo: number;
    warden: string;
}

const response: ApiResponse<HostelDetail> = {
    statusCode: 200,
    data: { roomNo: 105, warden: "Dr. Prasad" }
};

console.log(`Status: ${response.statusCode}, Room: ${response.data.roomNo}`);
```
#### Output
```text
Status: 200, Room: 105
```
#### Explanation
`ApiResponse<T>` takes a type argument. Here we pass `HostelDetail` as `T`, which binds the `data` property to enforce `roomNo` and `warden` fields.

### Real World Usage
Parsing generic JSON payloads returned from backend endpoints:
```typescript
async function fetchResponse<T>(url: string): Promise<ApiResponse<T>> {}
```

### Common Mistakes
* **Overusing generics:** Using generics when explicit types would be simpler makes the code unnecessarily complex.

### Topic Summary
Generics use type parameter placeholders (`<T>`) to create reusable, type-safe operations.

---

## Type Assertions

### Definition
A **Type Assertion** is a mechanism that tells the TypeScript compiler to treat a variable as a specific target type, overriding the compiler's default inference. *(TypeScript compiler ko batana ki hume code ke runtime type ke baare me compiler se zyada pta hai (using `as` syntax).)*

### Key Concepts
* **No Runtime Cast:** Assertions are compile-time overrides; they do not alter the runtime value or behavior of the data.
* **Syntax Options:** Use the `as Type` syntax or `<Type>` prefix.

### Syntax
```typescript
const value: unknown = "VJTI";
const length = (value as string).length;
```

### Example
#### Code
```typescript
type Student = { name: string; rollNo: number };

// Simulating database return typed as 'any'
const rawResponse: any = { name: "Rahul Verma", rollNo: 1005, role: "Representative" };

// Assert that rawResponse matches Student structure
const verifiedStudent = rawResponse as Student;

console.log(`Verified Student: ${verifiedStudent.name}`);
// console.log(verifiedStudent.role); // Compilation Error: 'role' does not exist on type 'Student'
```
#### Output
```text
Verified Student: Rahul Verma
```
#### Explanation
Using `as Student` overrides the compiler's `any` typing, restricting variable properties to the `Student` interface while writing code.

### Real World Usage
Casting web elements located on a page to specialized type tags:
```typescript
const linkElement = page.locator("a#home") as HTMLAnchorElement;
```

### Common Mistakes
* **Forcing incorrect type assertions:** Asserting an object is of a type that it does not match at runtime can lead to silent errors.

### Topic Summary
Type Assertions (`as`) override the compiler's default type inferences during development.

---

## Utility Types

### Definition
**Utility Types** are built-in type mapping tools in TypeScript that allow transforming and manipulating existing type structures. *(Pehle se bane types ko customize karne ke built-in classes, jaise Partial ya Readonly.)*

### Key Concepts
* **`Partial<T>`:** Makes all properties of type `T` optional.
* **`Readonly<T>`:** Makes all properties of type `T` read-only (un-assignable).
* **`Pick<T, K>`:** Constructs a type by picking a set of properties `K` from type `T`.

### Syntax
```typescript
const updateProfile: Partial<Student> = { name: "Rahul" };
```

### Example
#### Code
```typescript
interface UserInfo {
    username: string;
    branch: string;
    gpa: number;
}

// 1. Partial: All properties optional
const tempRecord: Partial<UserInfo> = { username: "Priya" };

// 2. Readonly: Properties cannot be modified
const fixedRecord: Readonly<UserInfo> = {
    username: "Vikram",
    branch: "IT",
    gpa: 9.5
};

// fixedRecord.gpa = 9.8; // Error: Cannot assign to 'gpa' because it is a read-only property.

console.log("Partial Username:", tempRecord.username);
console.log("Readonly Username:", fixedRecord.username);
```
#### Output
```text
Partial Username: Priya
Readonly Username: Vikram
```
#### Explanation
1. `Partial<UserInfo>` allows declaring `tempRecord` with only the `username` property.
2. `Readonly<UserInfo>` blocks property mutations on `fixedRecord`.

### Real World Usage
Managing option sets or configuration payload overrides where fields are optional:
```typescript
function runTest(options: Partial<TestOptions>) {}
```

### Common Mistakes
* **Modifying a Readonly object at runtime:** TypeScript blocks code compilation if you mutate a `Readonly` type, but does not freeze the object dynamically in Javascript. Use `Object.freeze()` to enforce runtime lock.

### Topic Summary
Utility Types (`Partial`, `Readonly`) customize existing type structures easily.

---

## SOLID Principles (Introduction)

### Definition
**SOLID** is a mnemonic acronym for five object-oriented design principles that help software developers design readable, maintainable, and scalable systems. *(Readable, maintainable, aur reusable software design ke paanch solid rules.)*

### Key Concepts
* **S - Single Responsibility Principle (SRP):** A class should have one, and only one, reason to change (do one job).
* **O - Open-Closed Principle (OCP):** Software entities should be open for extension but closed for modification.
* **L - Liskov Substitution Principle (LSP):** Child classes should be substitutable for their parent classes without breaking code.
* **I - Interface Segregation Principle (ISP):** Clients should not be forced to depend on interfaces they do not use (prefer small, focused interfaces).
* **D - Dependency Inversion Principle (DIP):** Depend on abstractions, not on concrete classes.

### Visual Explanation
Analogy: Canteen kitchen operations.
```text
Violates SRP: One single chef prepares food, washes dishes, takes orders, and cleans tables.
Follows SRP: Cook A prepares food, Cleaner B cleans tables, Cashier C takes orders.
```

### Example (SRP Refactoring)
#### Violates SRP
```typescript
class StudentManager {
    addStudent(name: string) {
        console.log(`Added ${name} to database.`);
        // Violation: Class handles database operations AND logging
        this.writeLogToFile(`User ${name} added.`);
    }
    writeLogToFile(message: string) {
        console.log(`[FILE LOG]: ${message}`);
    }
}
```
#### Adheres to SRP
```typescript
class LoggerService {
    log(message: string) {
        console.log(`[FILE LOG]: ${message}`);
    }
}

class StudentManager {
    private logger = new LoggerService();

    addStudent(name: string) {
        console.log(`Added ${name} to database.`);
        this.logger.log(`User ${name} added.`); // Delegates logging
    }
}
```

### Real World Usage
Structuring Page Object Models where page interactions (e.g. click actions) are separated from test validations (assertions):
```typescript
// LoginPage class performs page actions only (SRP).
// Test assertion file verifies outcomes separately.
```

### Common Mistakes
* **Over-engineering:** Applying all SOLID principles to simple, 10-line scripts, creating unnecessary directories and class interfaces.

### Topic Summary
SOLID principles promote decoupled, modular class structures. SRP restricts a class to one job.

---

## Session Summary

### Key Takeaways
1. **Async Control Flow:** Promises represent future states (Pending, Resolved, Rejected). Async/Await wraps promises to write linear, readable code.
2. **Safety Gates:** `try-catch-finally` prevents exceptions from crashing scripts, while Modular imports split code bases cleanly.
3. **Type Assertions:** TypeScript adds compilation checks using explicit Types, Interfaces, and Enums, and customizes configurations via Utility Types.
4. **Design Structure:** Generics create reusable type models. SOLID principles promote modular page layouts in automation frameworks.

### Important Interview Points
* **What is the difference between `Promise.all` and sequential `await` calls?**
  * Sequential `await` pauses execution on each call. `Promise.all` runs promises in parallel, returning when all complete.
* **How do TypeScript Interfaces and Types differ?**
  * Interfaces define object structures and can be extended via inheritance. Types can model primitives, unions, and tuples.
* **Explain the Single Responsibility Principle.**
  * SRP states a class should have only one reason to change, isolating features into separate units to prevent regression bugs.
