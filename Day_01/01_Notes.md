# Day 1: JavaScript Fundamentals

## Session Plan

| Topic                  | Estimated Time |
| ---------------------- | -------------- |
| Variables              | 15 mins        |
| Data Types             | 15 mins        |
| Operators              | 15 mins        |
| Conditional Statements | 15 mins        |
| Loops                  | 20 mins        |
| Functions              | 15 mins        |
| Arrays                 | 15 mins        |
| Objects                | 10 mins        |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Declare, initialize, and modify variables using `let`, `const`, and `var`.
* Identify and use JavaScript's primitive and non-primitive data types.
* Perform calculations, comparisons, and logical checks using operators.
* Write decision-making logic using conditional blocks (`if-else`, `switch`).
* Automate repetitive tasks using loops (`for`, `while`).
* Write reusable code using functions with parameters and return values.
* Create and manipulate ordered lists of data using arrays.
* Represent real-world entities using key-value pairs in objects.

---

## Introduction

Welcome to the foundation of test automation. JavaScript is the programming language that powers modern web development, and it is the primary language used to write robust, high-performance automation tests in Playwright.

In this module, we will cover the absolute essentials of JavaScript. Understanding variables, conditionals, functions, arrays, and objects is crucial because every automated test script you write is essentially a JavaScript program. Whether you are extracting text from a web page, looping through table rows, or parsing API responses, you will rely directly on these fundamental building blocks.

---

## Variables

### Definition
A **variable** is a named container in memory used to store data that a program can manipulate during execution.

### Key Concepts
In JavaScript, variables are declared using three keywords: `let`, `const`, and `var`. 

* **Declaration vs Initialization:**
  * *Declaration:* Telling the computer that a variable exists (e.g., `let username;`).
  * *Initialization:* Assigning an initial value to the variable (e.g., `username = "admin";`).
* **The Three Keywords:**
  * `const`: Used for values that must remain constant. They cannot be reassigned or re-declared. You must initialize a `const` when declaring it.
  * `let`: Used for variables whose values are expected to change. It is block-scoped, meaning it only exists within the `{}` block where it is defined.
  * `var`: The legacy way to declare variables. It is function-scoped or globally-scoped, which can lead to bugs due to lack of block scoping. Avoid using `var` in modern code.

| Keyword | Re-assignable | Re-declarable | Scope |
| ------- | ------------- | ------------- | ----- |
| `const` | No            | No            | Block |
| `let`   | Yes           | No            | Block |
| `var`   | Yes           | Yes           | Function/Global |

### Syntax
```javascript
// Declaring and initializing a constant
const baseUrl = "https://example.com";

// Declaring and initializing a re-assignable variable
let loginAttempts = 0;

// Re-assigning a let variable
loginAttempts = 1;
```

### Visual Explanation
Imagine variables as labeled storage boxes in a warehouse:
```
Memory space
┌─────────────────────────┐
│  ┌─────────┐            │
│  │ "admin" │  ◄─── [Box Label: username] (const)
│  └─────────┘            │
│  ┌─────────┐            │
│  │    0    │  ◄─── [Box Label: loginAttempts] (let)
│  └─────────┘            │
└─────────────────────────┘
```

### Example
#### Code
```javascript
const maxRetries = 3;
let currentAttempt = 1;

console.log("Max Retries configured: " + maxRetries);
console.log("Starting attempt: " + currentAttempt);

currentAttempt = 2; // Reassigning let
console.log("Moving to attempt: " + currentAttempt);

// The line below would throw an error if uncommented:
// maxRetries = 5; 
```
#### Output
```text
Max Retries configured: 3
Starting attempt: 1
Moving to attempt: 2
```
#### Explanation
1. `maxRetries` is declared as a constant with value `3`.
2. `currentAttempt` is declared using `let` with value `1`.
3. We successfully change the value of `currentAttempt` to `2`.
4. If we try to reassign `maxRetries`, JavaScript throws a `TypeError` because constants cannot be reassigned.

### Real World Usage
In Playwright test scripts, variables store environment settings, element texts, locators, and counters:
```javascript
const loginUrl = "https://app.saucedemo.com";
let loginStatus = "pending";
```

### Common Mistakes
* **Attempting to reassign a `const` variable:**
  * *Why it happens:* Forgetting that a variable was defined as a constant.
  * *How to avoid:* Default to `const` for all variables. Only use `let` if you know you need to change its value.
* **Declaring a `const` without initialization:**
  * *Why it happens:* Syntax error in writing.
  * *How to avoid:* Always write `const name = value;`.

### Topic Summary
Variables act as memory locations. Modern JavaScript uses `const` for values that do not change and `let` for values that do. Avoid using `var`.

---

## Data Types

### Definition
A **data type** determines what kind of value a variable holds and how the JavaScript engine handles operations on that value.

### Key Concepts
JavaScript variables are dynamic, meaning they can hold any data type and change types during runtime (though code readability suffers if done excessively). JavaScript data types are divided into two main categories:

1. **Primitive Data Types** (Stored directly in the stack memory, immutable):
   * **String:** Textual data enclosed in quotes (e.g., `"Playwright"`, `'Automation'`).
   * **Number:** Numeric values, both integers and floating-point decimals (e.g., `10`, `99.99`).
   * **Boolean:** Logical entity representing `true` or `false`.
   * **Undefined:** A variable that has been declared but not assigned a value.
   * **Null:** An intentional absence of any object value.
2. **Non-Primitive Data Types** (Stored by reference in heap memory, mutable):
   * **Object:** A collection of related properties.
   * **Array:** A list-like ordered collection.

The `typeof` operator is used to determine the data type of a variable or value.

### Syntax
```javascript
let myString = "Test Automation";
let myNumber = 101;
let myBoolean = true;
let myUndefined;
let myNull = null;
```

### Visual Explanation
Memory allocation differences:
```
┌─────────────────────────────────┐
│ Stack (Primitives)              │
│ age ──► [ 25 ] (Value)          │
│                                 │
│ Heap (Non-Primitives)           │
│ user ──► [Ref Address 0x1A] ────┼───┐
└─────────────────────────────────┘   │
                                      ▼
                             ┌────────────────┐
                             │ { name: "Max" }│
                             └────────────────┘
```

### Example
#### Code
```javascript
let appName = "Playwright Runner";
let port = 8080;
let isServerRunning = false;
let config;
let sessionToken = null;

console.log("Type of appName:", typeof appName);
console.log("Type of port:", typeof port);
console.log("Type of isServerRunning:", typeof isServerRunning);
console.log("Type of config:", typeof config);
console.log("Type of sessionToken:", typeof sessionToken);
```
#### Output
```text
Type of appName: string
Type of port: number
Type of isServerRunning: boolean
Type of config: undefined
Type of sessionToken: object
```
#### Explanation
* Primitive values return their direct type names.
* Note that `typeof null` returns `"object"`. This is a well-known legacy bug in JavaScript, but it is treated as a primitive value.
* `config` is declared but uninitialized, so its type is `"undefined"`.

### Real World Usage
When verifying a UI table entry:
* A price is parsed as a string (`"$49.99"`), which must be converted to a `Number` (`49.99`) for calculations.
* Element visibility returns a `Boolean` (`true` or `false`).

### Common Mistakes
* **Treating null and undefined as identical:**
  * *Why it happens:* Both represent empty values.
  * *How to avoid:* Remember that `undefined` means a variable has not been initialized yet, while `null` is explicitly assigned by the developer to represent "no value".
* **Implicit string concatenation during math operations:**
  * *Why it happens:* Adding a number to a string (e.g., `"10" + 5` evaluates to `"105"` instead of `15`).
  * *How to avoid:* Explicitly convert strings to numbers using `Number()` before mathematical operations.

### Topic Summary
JavaScript contains primitives (String, Number, Boolean, Undefined, Null) and non-primitives (Object, Array). Use `typeof` to inspect variable types.

---

## Operators

### Definition
An **operator** is a symbol that instructs the JavaScript engine to perform mathematical, comparison, or logical manipulations on variables and values.

### Key Concepts
* **Arithmetic Operators:** Perform mathematical calculations.
  * `+` (Addition), `-` (Subtraction), `*` (Multiplication), `/` (Division), `%` (Modulus/Remainder), `**` (Exponentiation).
* **Assignment Operators:** Assign values to variables.
  * `=` (Assignment), `+=` (Add and assign), `-=` (Subtract and assign).
* **Comparison Operators:** Compare two values and return a boolean.
  * `==` (Loose equality - compares values only, coerces types).
  * `===` (Strict equality - compares values and types, highly recommended).
  * `!=` (Loose inequality), `!==` (Strict inequality).
  * `>`, `<`, `>=`, `<=`.
* **Logical Operators:** Combine multiple conditions.
  * `&&` (Logical AND): Returns true if both conditions are true.
  * `||` (Logical OR): Returns true if at least one condition is true.
  * `!` (Logical NOT): Inverts a boolean value.

### Syntax
```javascript
let sum = 10 + 5;        // Arithmetic
let count = 0;
count += 1;              // Assignment
let isEqual = (5 === 5); // Comparison
let isBothTrue = (5 > 2) && (10 < 20); // Logical
```

### Visual Explanation
The difference between `==` and `===`:
```
Loose Equality (5 == "5")
Value: 5 vs "5" ──► Auto Type Coercion ──► Value: 5 vs 5 ──► TRUE

Strict Equality (5 === "5")
Value Check: 5 vs "5" ──► Value matches? (Yes)
Type Check: number vs string ──► Type matches? (No) ──► FALSE
```

### Example
#### Code
```javascript
let totalItems = 10;
let itemsPerPage = "10";

console.log("Loose Match:", totalItems == itemsPerPage);
console.log("Strict Match:", totalItems === itemsPerPage);

let hasCoupon = true;
let isMember = false;

// Check if they are eligible for discount
let getDiscount = hasCoupon || isMember;
console.log("Eligible for Discount:", getDiscount);
```
#### Output
```text
Loose Match: true
Strict Match: false
Eligible for Discount: true
```
#### Explanation
* `totalItems == itemsPerPage` is `true` because loose equality converts the string `"10"` to a number before comparing.
* `totalItems === itemsPerPage` is `false` because their data types (`number` and `string`) are different.
* `hasCoupon || isMember` evaluates to `true` because at least one of the logical operands is `true`.

### Real World Usage
* Checking test run statistics: `passCount + failCount === totalTests`.
* Writing complex conditions: `let shouldRun = isSmokeTest && !isMobileBrowser`.

### Common Mistakes
* **Using `==` instead of `===`:**
  * *Why it happens:* Unawareness of type coercion rules.
  * *How to avoid:* Always use `===` and `!==` to prevent unpredictable type conversion errors.

### Topic Summary
Operators allow you to calculate, assign, compare, and apply logical checks. Always default to strict equality (`===`).

---

## Conditional Statements

### Definition
**Conditional statements** are control structures that execute specific blocks of code depending on whether a given condition evaluates to `true` or `false`.

### Key Concepts
* **`if` Statement:** Runs a block of code if the condition is true.
* **`if-else` Statement:** Runs one block of code if the condition is true, and another block if it is false.
* **`else if` Ladder:** Checks multiple conditions sequentially.
* **`switch` Statement:** Compares a variable against multiple static values (`cases`) for cleaner multi-branch paths.

### Syntax
```javascript
// if-else
if (condition) {
    // runs if condition is true
} else {
    // runs if condition is false
}

// switch
switch (expression) {
    case value1:
        // code block
        break;
    case value2:
        // code block
        break;
    default:
        // default code block
}
```

### Visual Explanation
```
              [ Condition Check ]
                 /           \
         (True) /             \ (False)
               ▼               ▼
        [ Run If-Block ]     [ Run Else-Block ]
               \               /
                \             /
                 ▼           ▼
             [ Continue Program ]
```

### Example
#### Code
```javascript
let testStatus = "failed";

if (testStatus === "passed") {
    console.log("Status: Green. Deploying build.");
} else if (testStatus === "failed") {
    console.log("Status: Red. Alerting engineering team.");
} else {
    console.log("Status: Yellow. Status unknown.");
}

let browser = "firefox";

switch (browser) {
    case "chrome":
        console.log("Launching Chrome Browser");
        break;
    case "firefox":
        console.log("Launching Firefox Browser");
        break;
    default:
        console.log("Unsupported browser specified");
}
```
#### Output
```text
Status: Red. Alerting engineering team.
Launching Firefox Browser
```
#### Explanation
1. `testStatus` is checked. Since it matches `"failed"`, the second block in the `else if` ladder executes.
2. The `switch` evaluates the variable `browser`. It matches `case "firefox"`, prints the log, and hits the `break` statement to exit the switch structure.

### Real World Usage
Determining browser behavior or environment configurations in scripts:
```javascript
if (process.env.ENV === "staging") {
    baseUrl = "https://staging.app.com";
} else {
    baseUrl = "https://production.app.com";
}
```

### Common Mistakes
* **Using single `=` instead of `===` inside condition:**
  * *Why it happens:* Accidentally assigning a value inside the parenthesis: `if (status = "passed")`. This evaluates to the value itself (which is truthy), executing the block.
  * *How to avoid:* Double-check your code; always use comparisons (`===`) inside condition checks.
* **Forgetting the `break` statement in switch cases:**
  * *Why it happens:* Oversight.
  * *How to avoid:* Remember that without a `break`, the engine will "fall through" and execute subsequent case blocks even if the case labels do not match.

### Topic Summary
Conditional statements route code execution. Use `if-else` for dynamic range checks and `switch` for static multi-value matching.

---

## Loops

### Definition
A **loop** is a control flow statement that repeatedly executes a block of code as long as a specified condition remains true.

### Key Concepts
* **`for` Loop:** Ideal when the number of iterations is known beforehand. It uses a counter variable, a loop condition, and an increment/decrement step.
* **`while` Loop:** Ideal when the number of iterations is not known in advance. It runs as long as its condition remains true.
* **Loop Control Statements:**
  * `break`: Immediately exits the loop.
  * `continue`: Skips the current iteration and jumps to the next loop evaluation.

### Syntax
```javascript
// for loop
for (initialization; condition; increment) {
    // code block
}

// while loop
while (condition) {
    // code block
}
```

### Visual Explanation
Loop execution cycle:
```
           ┌───► [ Evaluate Condition ]
           │          /           \
           │  (True) /             \ (False)
           │        ▼               ▼
           │  [ Run Code Block ]   [ Exit Loop ]
           │        │
           └─── [ Increment ]
```

### Example
#### Code
```javascript
console.log("For Loop Example:");
for (let i = 1; i <= 3; i++) {
    console.log("Iteration number: " + i);
}

console.log("\nWhile Loop Example:");
let retriesLeft = 2;
while (retriesLeft > 0) {
    console.log("Retrying action... Retries left: " + retriesLeft);
    retriesLeft--;
}
```
#### Output
```text
For Loop Example:
Iteration number: 1
Iteration number: 2
Iteration number: 3

While Loop Example:
Retrying action... Retries left: 2
Retrying action... Retries left: 1
```
#### Explanation
1. In the `for` loop, `i` starts at `1`. The loop runs, prints, increments `i`, and repeats until `i` is `4` (making `i <= 3` evaluate to `false`).
2. In the `while` loop, the block runs because `retriesLeft` starts at `2` (which is `> 0`). The variable is decremented inside the loop. The loop terminates when `retriesLeft` reaches `0`.

### Real World Usage
* Retrying a failed test action until it succeeds or reaches a limit.
* Scraping rows of data from an HTML table.

### Common Mistakes
* **Creating an Infinite Loop:**
  * *Why it happens:* Forgetting to update the loop counter or check variable inside the loop body, causing the condition to remain `true` forever.
  * *How to avoid:* Ensure the loop control variable is modified within the block (e.g., `retriesLeft--`).
* **Off-by-one errors:**
  * *Why it happens:* Incorrect comparison operators (e.g., `<` vs `<=`).
  * *How to avoid:* Trace the loop manually for the boundary values (e.g. index `0` and index `length`).

### Topic Summary
Loops automate repetitive operations. Use `for` loops for counter-based iteration and `while` loops for state/condition-based execution.

---

## Functions

### Definition
A **function** is a self-contained block of reusable code designed to perform a specific task, executed only when invoked (called).

### Key Concepts
* **Function Declaration:** Declaring a function using the `function` keyword.
* **Function Expression:** Defining a function and assigning it to a variable.
* **Parameters and Arguments:**
  * *Parameters:* Variables listed in the function definition (placeholders).
  * *Arguments:* Actual values passed to the function when it is invoked.
* **Return Statement:** Stops execution of the function and outputs a value back to the caller. If omitted, the function returns `undefined`.

### Syntax
```javascript
// Function Declaration
function greetUser(name) {
    return "Hello, " + name + "!";
}

// Function Expression
const calculateTotal = function(price, count) {
    return price * count;
};

// Calling (invoking)
let greeting = greetUser("Alice");
```

### Visual Explanation
Functions behave like data processing machines:
```
Inputs (Arguments) ──► [ Function Box (Logic) ] ──► Output (Return Value)
```

### Example
#### Code
```javascript
// Declaration
function computeTax(amount, rate) {
    let tax = amount * (rate / 100);
    return tax;
}

// Invocation
let totalBill = 250;
let taxDue = computeTax(totalBill, 8); // Pass arguments 250 and 8

console.log("Tax Due on bill:", taxDue);
console.log("Total Amount:", totalBill + taxDue);
```
#### Output
```text
Tax Due on bill: 20
Total Amount: 270
```
#### Explanation
1. `computeTax` is defined with parameters `amount` and `rate`.
2. When called with `250` and `8`, these values replace the parameters inside the block.
3. The function calculates the tax value (`20`) and sends it back via the `return` statement.
4. The returned value is saved to `taxDue` and used in console outputs.

### Real World Usage
Encapsulating test actions to avoid code repetition:
```javascript
function login(username, password) {
    // Locating elements and entering data steps
}
```

### Common Mistakes
* **Forgetting to write the `return` statement:**
  * *Why it happens:* Assuming the last line is automatically returned.
  * *How to avoid:* Explicitly type `return` when you want a value sent back.
* **Confusing parameters with arguments:**
  * *Why it happens:* Mixing up definition terms with runtime values.
  * *How to avoid:* Remember that parameters are defined in the function signature, while arguments are passed during the call.

### Topic Summary
Functions prevent code duplication. They take inputs (arguments), process logic, and return outputs.

---

## Arrays

### Definition
An **array** is an ordered, zero-indexed collection of values that allows storing multiple items under a single variable name.

### Key Concepts
* **Zero-Indexing:** The first element is stored at index `0`, the second at index `1`, etc.
* **Length Property:** `array.length` returns the total number of items in the array.
* **Basic Array Methods:**
  * `push()`: Adds an element to the end.
  * `pop()`: Removes the last element and returns it.
  * `shift()`: Removes the first element.
  * `unshift()`: Adds an element to the beginning.

### Syntax
```javascript
let testBrowsers = ["chromium", "firefox", "webkit"];
console.log(testBrowsers[0]); // Accessing "chromium"
```

### Visual Explanation
```
Array Variable: testBrowsers
┌──────────────┬──────────────┬──────────────┐
│  "chromium"  │  "firefox"   │   "webkit"   │  ◄── Elements
└──────────────┴──────────────┴──────────────┘
    Index 0        Index 1        Index 2       (Length = 3)
```

### Example
#### Code
```javascript
let fruits = ["Apple", "Banana"];
console.log("Initial List:", fruits);

// Push to append
fruits.push("Orange");
console.log("After push:", fruits);

// Pop to remove end item
let lastFruit = fruits.pop();
console.log("Popped element:", lastFruit);
console.log("Final List:", fruits);
console.log("List length:", fruits.length);
```
#### Output
```text
Initial List: [ 'Apple', 'Banana' ]
After push: [ 'Apple', 'Banana', 'Orange' ]
Popped element: Orange
Final List: [ 'Apple', 'Banana' ]
List length: 2
```
#### Explanation
1. `fruits` is initialized with two elements.
2. `fruits.push("Orange")` appends `"Orange"` as the third element.
3. `fruits.pop()` removes `"Orange"` from the end and returns it.
4. `fruits.length` reflects the size of the array, which returns `2` after the pop.

### Real World Usage
Arrays store list data retrieved from pages or list elements:
```javascript
let errorMessages = ["Invalid Username", "Password is too short", "Email is invalid"];
```

### Common Mistakes
* **Accessing an invalid index:**
  * *Why it happens:* Trying to access `array[array.length]`. Since arrays are zero-indexed, the last element is at `array.length - 1`.
  * *How to avoid:* Always use `index < array.length` when iterating, and remember indices end at `length - 1`.

### Topic Summary
Arrays are index-based lists of items. Use methods like `push` and `pop` to modify elements, and check length with `.length`.

---

## Objects

### Definition
An **object** is a standalone entity containing a collection of related properties, structured as `key: value` pairs.

### Key Concepts
* **Properties and Methods:**
  * *Properties:* Variables inside an object that describe it.
  * *Methods:* Functions defined inside an object that represent actions it can perform.
* **Accessing Property Values:**
  * *Dot Notation:* `objectName.propertyName` (cleanest and most common).
  * *Bracket Notation:* `objectName["propertyName"]` (required if property name has spaces, special characters, or is stored in a variable).
* **Object Modification:**
  * Properties can be added, updated, or deleted dynamically using assignment and the `delete` keyword.

### Syntax
```javascript
// Object Declaration
let user = {
    username: "qa_tester",
    role: "Admin"
};

// Accessing properties
console.log(user.username);
console.log(user["role"]);
```

### Visual Explanation
```
Object: user
┌────────────────────────────┐
│  Key          Value        │
│ ┌───────────┬────────────┐ │
│ │ username  │ "qa_tester"│ │
│ ├───────────┼────────────┤ │
│ │ role      │ "Admin"    │ │
│ └───────────┴────────────┘ │
└────────────────────────────┘
```

### Example
#### Code
```javascript
let config = {
    env: "QA",
    timeout: 30000
};

console.log("Initial Config:", config);

// Update property value
config.timeout = 50000;

// Add new property
config.headless = true;

// Delete property
delete config.env;

console.log("Updated Config:", config);
```
#### Output
```text
Initial Config: { env: 'QA', timeout: 30000 }
Updated Config: { timeout: 50000, headless: true }
```
#### Explanation
1. An object named `config` is created with properties `env` and `timeout`.
2. `config.timeout = 50000` changes the value of `timeout`.
3. `config.headless = true` inserts a new property dynamically.
4. `delete config.env` removes the `env` property.
5. The final printed object displays these changes.

### Real World Usage
Objects represent configuration blocks, credentials, and JSON response bodies:
```javascript
const testUser = {
    email: "tester@example.com",
    password: "SecurePassword123"
};
```

### Common Mistakes
* **Using undefined variables in bracket notation:**
  * *Why it happens:* Writing `config[timeout]` instead of `config["timeout"]`. JavaScript looks for a variable called `timeout` rather than using the string literal `"timeout"`.
  * *How to avoid:* Use dot notation whenever possible. If using brackets, wrap keys in quotes.

### Topic Summary
Objects store keyed properties. Read or write values using dot or bracket notation, and add or delete properties dynamically.

---

## Session Summary

### Key Takeaways
1. Use `const` by default to declare constants, and switch to `let` only for variable reassignment. Avoid `var`.
2. Distinguish primitive types (stored by value) from non-primitive objects and arrays (stored by reference).
3. Use strict equality (`===`) to ensure value and type are equivalent.
4. Manage program flows through conditional blocks (`if-else`) and loops (`for`/`while`).
5. Write functions to isolate reusable logic blocks.
6. Group items in arrays using index retrieval, and group characteristics in objects using key retrieval.

### Important Interview Points
* **What is the difference between `let` and `const`?**
  * `const` variables are block-scoped and cannot be reassigned or re-declared. `let` variables are also block-scoped but can be reassigned.
* **Explain the difference between `==` and `===`.**
  * `==` checks value equality after automatic type conversion. `===` checks both value and data type without conversion.
* **What is the difference between `null` and `undefined`?**
  * `undefined` is a default state when a variable has been declared but not initialized. `null` is an intentional value representing empty or nothing.
* **Explain Stack vs Heap representation.**
  * Primitive values are stored directly in the Stack memory because their size is fixed. Objects and Arrays are stored in Heap memory because their size can change; the Stack holds only the reference address of the Heap location.

### Quick Revision Sheet

* **Variables:**
  ```javascript
  const count = 5; // Reassignment forbidden
  let age = 30;    // Reassignment allowed
  ```
* **Data Types:**
  * Primitives: String, Number, Boolean, Undefined, Null
  * Non-Primitives: Object, Array
* **Operators:**
  * Strict comparison: `===`, `!==`
  * Logical operators: `&&` (AND), `||` (OR), `!` (NOT)
* **Loops:**
  * `for` loops iterate a fixed number of times.
  * `while` loops repeat based on dynamic status.
* **Functions:**
  ```javascript
  function name(param) {
      return param; 
  }
  ```
* **Arrays & Objects:**
  * Array: Index-based ordered list (`[1, 2, 3]`).
  * Object: Key-value property collection (`{ id: 1 }`).
