# Day 1: JavaScript Fundamentals

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Variables | 15 mins |
| Data Types | 15 mins |
| Operators | 15 mins |
| Conditional Statements | 15 mins |
| Loops | 20 mins |
| Functions | 15 mins |
| Arrays | 15 mins |
| Objects | 10 mins |

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
A **variable** is a named container in memory used to store data that a program can manipulate during execution. *(Ek naam wala dabba jo data store karta hai.)*

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
Imagine variables as labeled dabbas (containers) in your kitchen — the label tells you what's inside:
```text
Kitchen Shelf (Memory)
┌──────────────────────────────────────────┐
│  ┌───────────┐                           │
│  │  "chai"   │  ◄─── [Dabba Label: drinkName] (const - sealed jar)
│  └───────────┘                           │
│  ┌───────────┐                           │
│  │     0     │  ◄─── [Dabba Label: cupsServed] (let - open container)
│  └───────────┘                           │
└──────────────────────────────────────────┘
```

### Example
#### Code
```javascript
const collegeName = "VJTI Mumbai";
let semesterFee = 45000;

console.log("College: " + collegeName);
console.log("Current Semester Fee: " + semesterFee);

semesterFee = 48000; // Fee increased — reassigning let
console.log("Updated Semester Fee: " + semesterFee);

// The line below would throw an error if uncommented:
// collegeName = "IIT Bombay"; // TypeError: Assignment to constant variable
```
#### Output
```text
College: VJTI Mumbai
Current Semester Fee: 45000
Updated Semester Fee: 48000
```
#### Explanation
1. `collegeName` is declared as a constant — once you join a college, the name doesn't change.
2. `semesterFee` is declared using `let` — fees can increase or decrease.
3. We successfully updated `semesterFee` from `45000` to `48000`.
4. If we try to reassign `collegeName`, JavaScript throws a `TypeError` because constants cannot be reassigned.

### Real World Usage
Variables are used everywhere — from storing your UPI transaction ID to configuring test scripts:
```javascript
// In a college management app
const universityName = "Savitribai Phule Pune University";
let studentCount = 350;

// In Playwright automation scripts
const loginUrl = "https://app.saucedemo.com";
let loginStatus = "pending";
```

### Common Mistakes
* **Attempting to reassign a `const` variable:** Forgetting that a variable was defined as a constant. Default to `const` for all variables. Only use `let` if you know you need to change its value.
* **Declaring a `const` without initialization:** Always write `const name = value;`.

### Topic Summary
Variables act as memory locations. Modern JavaScript uses `const` for values that do not change and `let` for values that do. Avoid using `var`.

---

## Data Types

### Definition
A **data type** determines what kind of value a variable holds and how the JavaScript engine handles operations on that value. *(Data type batata hai ki variable ke andar kis tarah ka data hai — number hai, text hai, ya true/false hai.)*

### Key Concepts
JavaScript variables are dynamic, meaning they can hold any data type and change types during runtime. JavaScript data types are divided into two main categories:

1. **Primitive Data Types** (Stored directly in the stack memory, immutable):
   * **String:** Textual data enclosed in quotes (e.g., `"Playwright"`).
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
let studentName = "Priya Sharma";   // String
let semesterMarks = 78;              // Number
let isPassed = true;                 // Boolean
let backlogSubject;                  // Undefined (not assigned yet)
let scholarshipAmount = null;        // Null (intentionally empty)
```

### Visual Explanation
Memory allocation differences:
```text
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
let studentName = "Rahul Patil";
let rollNumber = 42;
let hasScholarship = false;
let hostelRoom;
let pendingFee = null;

console.log("Type of studentName:", typeof studentName);
console.log("Type of rollNumber:", typeof rollNumber);
console.log("Type of hasScholarship:", typeof hasScholarship);
console.log("Type of hostelRoom:", typeof hostelRoom);
console.log("Type of pendingFee:", typeof pendingFee);
```
#### Output
```text
Type of studentName: string
Type of rollNumber: number
Type of hasScholarship: boolean
Type of hostelRoom: undefined
Type of pendingFee: object
```
#### Explanation
* Primitive values return their direct type names.
* `hostelRoom` is declared but no room is assigned yet, so its type is `"undefined"` — like a student who hasn't received hostel allotment.
* Note that `typeof null` returns `"object"`. This is a well-known legacy bug in JavaScript. `pendingFee` is `null` because we intentionally set it to mean "no pending fee".
* 🔥 **Interview Tip:** "What does `typeof null` return?" is a very common JavaScript interview question.

### Real World Usage
When building real applications:
* A student's semester fee displayed on a college portal is a string (`"₹45,000"`), which must be converted to a `Number` (`45000`) for calculations.
* A bus seat's availability on IRCTC returns a `Boolean` (`true` or `false`).
* A Flipkart product price is stored as a `Number` for calculating discounts.

### Common Mistakes
* **Treating null and undefined as identical:** `undefined` means a variable has not been initialized yet, while `null` is explicitly assigned by the developer to represent "no value".
* **Implicit string concatenation during math operations:** Adding a number to a string (e.g., `"10" + 5` evaluates to `"105"` instead of `15`). Explicitly convert strings to numbers using `Number()` before mathematical operations.

### Topic Summary
JavaScript contains primitives (String, Number, Boolean, Undefined, Null) and non-primitives (Object, Array). Use `typeof` to inspect variable types.

---

## Operators

### Definition
An **operator** is a symbol that instructs the JavaScript engine to perform mathematical, comparison, or logical manipulations on variables and values. *(Operator ek symbol hai jo values ke upar calculation, comparison ya logical check karta hai.)*

### Key Concepts
* **Arithmetic Operators:** Perform mathematical calculations (`+`, `-`, `*`, `/`, `%`, `**`).
* **Assignment Operators:** Assign values to variables (`=`, `+=`, `-=`).
* **Comparison Operators:** Compare two values and return a boolean.
  * `==` (Loose equality - compares values only, coerces types).
  * `===` (Strict equality - compares values and types, highly recommended).
  * `!=` (Loose inequality), `!==` (Strict inequality).
  * `>`, `<`, `>=`, `<=`.
* **Logical Operators:** Combine multiple conditions (`&&` for AND, `||` for OR, `!` for NOT).

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
```text
Loose Equality (5 == "5")
Value: 5 vs "5" ──► Auto Type Coercion ──► Value: 5 vs 5 ──► TRUE

Strict Equality (5 === "5")
Value Check: 5 vs "5" ──► Value matches? (Yes)
Type Check: number vs string ──► Type matches? (No) ──► FALSE
```

### Example
#### Code
```javascript
let totalStudents = 60;
let sectionCapacity = "60";

console.log("Loose Match:", totalStudents == sectionCapacity);
console.log("Strict Match:", totalStudents === sectionCapacity);

let hasScholarship = true;
let isHosteler = false;

// Check if student is eligible for fee concession
let getsConcession = hasScholarship || isHosteler;
console.log("Eligible for Concession:", getsConcession);
```
#### Output
```text
Loose Match: true
Strict Match: false
Eligible for Concession: true
```
#### Explanation
* `totalStudents == sectionCapacity` is `true` because loose equality converts the string `"60"` to a number before comparing.
* `totalStudents === sectionCapacity` is `false` because their data types (`number` and `string`) are different.
* `hasScholarship || isHosteler` evaluates to `true` because at least one condition is `true` (scholarship exists).
* 🔥 **Interview Tip:** "What is the difference between `==` and `===`?" is asked in almost every JavaScript interview.

### Real World Usage
* Checking test run statistics: `passCount + failCount === totalTests`.
* Writing complex conditions: `let shouldRun = isSmokeTest && !isMobileBrowser`.

### Common Mistakes
* **Using `==` instead of `===`:** Unawareness of type coercion rules. Always use `===` and `!==` to prevent unpredictable type conversion errors.

### Topic Summary
Operators allow you to calculate, assign, compare, and apply logical checks. Always default to strict equality (`===`).

---

## Conditional Statements

### Definition
**Conditional statements** are control structures that execute specific blocks of code depending on whether a given condition evaluates to `true` or `false`. *(Conditional statement ka matlab hai — agar ye sach hai toh ye karo, nahi toh woh karo — jaise railway ticket checker valid ticket check karta hai.)*

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
    default:
        // default code block
}
```

### Visual Explanation
```text
               [ Condition Check ]
                  /           \
          (True) /             \ (False)
                ▼               ▼
         [ Run If-Block ]     [ Run Else-Block ]
                \               /
                 ▼             ▼
             [ Continue Program ]
```

### Example
#### Code
```javascript
let examResult = "fail";

if (examResult === "pass") {
    console.log("Result: Passed! Eligible for next semester.");
} else if (examResult === "fail") {
    console.log("Result: Failed. Apply for re-examination.");
} else {
    console.log("Result: Awaited. Check back later.");
}

let paymentMode = "UPI";

switch (paymentMode) {
    case "UPI":
        console.log("Processing UPI Payment via PhonePe/GPay");
        break;
    case "Card":
        console.log("Processing Card Payment");
        break;
    case "Cash":
        console.log("Processing Cash Payment at Counter");
        break;
    default:
        console.log("Unsupported payment mode");
}
```
#### Output
```text
Result: Failed. Apply for re-examination.
Processing UPI Payment via PhonePe/GPay
```
#### Explanation
1. `examResult` is checked. Since it matches `"fail"`, the second block in the `else if` ladder executes.
2. The `switch` evaluates `paymentMode`. It matches `case "UPI"`, prints the log, and the `break` statement exits the switch.
3. Without the `break`, the engine would "fall through" and execute subsequent case blocks even if they don't match.

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
* **Using single `=` instead of `===` inside condition:** Accidentally assigning a value inside the parenthesis: `if (status = "passed")`. This evaluates to the value itself (which is truthy), executing the block.
* **Forgetting the `break` statement in switch cases:** Without a `break`, the engine will "fall through" and execute subsequent case blocks even if the case labels do not match.

### Topic Summary
Conditional statements route code execution. Use `if-else` for dynamic range checks and `switch` for static multi-value matching.

---

## Loops

### Definition
A **loop** is a control flow statement that repeatedly executes a block of code as long as a specified condition remains true. *(Loop matlab ek kaam ko baar-baar karna jab tak condition true hai — jaise auto-rickshaw ka meter har kilometre pe tick karta hai.)*

### Key Concepts
* **`for` Loop:** Ideal when the number of iterations is known beforehand. It uses a counter variable, a loop condition, and an increment/decrement step.
* **`while` Loop:** Ideal when the number of iterations is not known in advance. It runs as long as its condition remains true.
* **Loop Control Statements:**
  * `break`: Immediately exits the loop.
  * `continue`: Skips the current iteration and jumps to the next loop evaluation.

### Syntax
```javascript
// for loop
for (let i = 0; i < 5; i++) {
    // code block
}

// while loop
while (condition) {
    // code block
}
```

### Visual Explanation
Loop execution cycle:
```text
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
* **Creating an Infinite Loop:** Forgetting to update the loop counter or check variable inside the loop body, causing the condition to remain `true` forever.
* **Off-by-one errors:** Incorrect comparison operators (e.g., `<` vs `<=`).

### Topic Summary
Loops automate repetitive operations. Use `for` loops for counter-based iteration and `while` loops for state/condition-based execution.

---

## Functions

### Definition
A **function** is a self-contained block of reusable code designed to perform a specific task, executed only when invoked (called). *(Function ek ATM machine jaisa hai — input do (card + PIN), process hota hai, aur output milta hai (cash).)*

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
```

### Visual Explanation
Functions work like an ATM machine — you provide inputs, it processes, and gives output:
```text
[ Card + PIN (Arguments) ] ──► [ ATM Machine (Function Logic) ] ──► [ Cash (Return Value) ]
```

### Example
#### Code
```javascript
function calculateGST(billAmount, gstRate) {
    let gst = billAmount * (gstRate / 100);
    return gst;
}

let messBill = 3500; // Monthly hostel mess bill
let gstAmount = calculateGST(messBill, 5); // 5% GST

console.log("Mess Bill:", messBill);
console.log("GST Amount:", gstAmount);
console.log("Total Payable:", messBill + gstAmount);
```
#### Output
```text
Mess Bill: 3500
GST Amount: 175
Total Payable: 3675
```
#### Explanation
1. `calculateGST` is defined with parameters `billAmount` and `gstRate`.
2. When called with `3500` and `5`, these values replace the parameters inside the block.
3. The function calculates GST (`175`) and sends it back via the `return` statement.
4. This pattern is used everywhere — from calculating semester fee GST to computing discounts on Flipkart orders.

### Real World Usage
Encapsulating test actions to avoid code repetition:
```javascript
function login(username, password) {
    // Locating elements and entering data steps
}
```

### Common Mistakes
* **Forgetting to write the `return` statement:** If a function is designed to output a result but lacks a `return` statement, it will return `undefined` by default.
* **Confusing parameters with arguments:** Parameters are defined in the function signature, while arguments are passed during the call.

### Topic Summary
Functions prevent code duplication. They take inputs (arguments), process logic, and return outputs.

---

## Arrays

### Definition
An **array** is an ordered, zero-indexed collection of values that allows storing multiple items under a single variable name. *(Array ek train jaisi hai — har coach ka ek seat number hota hai, aur sab ek line mein hote hain.)*

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
let semesterSubjects = ["Maths", "Physics", "Chemistry"];
console.log(semesterSubjects[0]); // Accessing "Maths"
```

### Visual Explanation
Think of an array like a train with numbered coaches — each coach (index) holds one passenger (value):
```text
Array Variable: semesterSubjects (Like a Train)
┌──────────────┬──────────────┬──────────────┐
│   "Maths"    │  "Physics"   │ "Chemistry"  │  ◄── Passengers (Elements)
└──────────────┴──────────────┴──────────────┘
   Coach 0        Coach 1        Coach 2        (Total Coaches = 3)
```

### Example
#### Code
```javascript
let hostelMenu = ["Poha", "Dal Rice"];
console.log("Today's Menu:", hostelMenu);

// Push to add item
hostelMenu.push("Chapati Sabzi");
console.log("After adding item:", hostelMenu);

// Pop to remove last item
let removedItem = hostelMenu.pop();
console.log("Removed item:", removedItem);
console.log("Final Menu:", hostelMenu);
console.log("Total items:", hostelMenu.length);
```
#### Output
```text
Today's Menu: [ 'Poha', 'Dal Rice' ]
After adding item: [ 'Poha', 'Dal Rice', 'Chapati Sabzi' ]
Removed item: Chapati Sabzi
Final Menu: [ 'Poha', 'Dal Rice' ]
Total items: 2
```
#### Explanation
1. `hostelMenu` starts with two items — like the mess menu board.
2. `hostelMenu.push("Chapati Sabzi")` adds a new item at the end.
3. `hostelMenu.pop()` removes the last item and returns it.
4. `hostelMenu.length` returns `2` — the current number of items on the menu.

### Real World Usage
Arrays store lists of items in real applications:
```javascript
// List of pending assignments in a college portal
let pendingAssignments = ["DSA Lab", "DBMS Project", "OS Viva"];

// List of validation errors on a form
let formErrors = ["Invalid Aadhar Number", "Mobile number must be 10 digits"];
```

### Common Mistakes
* **Accessing an invalid index:** Trying to access `array[array.length]`. Since arrays are zero-indexed, the last element is at `array.length - 1`. Always use `index < array.length` when iterating.

### Topic Summary
Arrays are index-based lists of items. Use methods like `push` and `pop` to modify elements, and check length with `.length`.

---

## Objects

### Definition
An **object** is a standalone entity containing a collection of related properties, structured as `key: value` pairs. *(Object ek Aadhar card jaisa hai — usme naam, DOB, address jaise fields hote hain, sab ek jagah.)*

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
// Object Declaration — like an Aadhar card with fields
let student = {
    name: "Amit Kumar",
    rollNumber: 42,
    branch: "Computer Science"
};

// Accessing properties
console.log(student.name);        // Dot notation
console.log(student["branch"]);   // Bracket notation
```

### Visual Explanation
Think of an object like an Aadhar card — it has labeled fields, each holding a value:
```text
Object: student (Like an Aadhar Card)
┌──────────────────────────────────────┐
│  Field (Key)      Value              │
│ ┌───────────────┬──────────────────┐ │
│ │ name          │ "Amit Kumar"     │ │
│ ├───────────────┼──────────────────┤ │
│ │ rollNumber    │ 42               │ │
│ ├───────────────┼──────────────────┤ │
│ │ branch        │ "Computer Sci"   │ │
│ └───────────────┴──────────────────┘ │
└──────────────────────────────────────┘
```

### Example
#### Code
```javascript
let studentProfile = {
    name: "Sneha Patil",
    branch: "Electronics",
    semester: 5
};

console.log("Initial Profile:", studentProfile);

// Update property value (branch change)
studentProfile.branch = "Computer Science";

// Add new property (hostel allotment)
studentProfile.hostel = "Girls Hostel B";

// Delete property
delete studentProfile.semester;

console.log("Updated Profile:", studentProfile);
```
#### Output
```text
Initial Profile: { name: 'Sneha Patil', branch: 'Electronics', semester: 5 }
Updated Profile: { name: 'Sneha Patil', branch: 'Computer Science', hostel: 'Girls Hostel B' }
```
#### Explanation
1. `studentProfile` is created with three key-value properties — like fields on a college ID card.
2. `studentProfile.branch = "Computer Science"` updates the branch (like a branch transfer).
3. `studentProfile.hostel = "Girls Hostel B"` adds a new field dynamically.
4. `delete studentProfile.semester` removes the semester property from the object.

### Real World Usage
Objects represent structured data everywhere — from student records to API responses:
```javascript
// Student record in a college ERP system
const studentRecord = {
    name: "Rahul Deshmukh",
    enrollmentId: "2024CS042",
    cgpa: 8.5
};

// UPI transaction response
const upiResponse = {
    transactionId: "TXN20240615001",
    status: "SUCCESS",
    amount: 500
};
```

### Common Mistakes
* **Using undefined variables in bracket notation:** Writing `config[timeout]` instead of `config["timeout"]`. JavaScript looks for a variable called `timeout` rather than using the string literal `"timeout"`. Use dot notation whenever possible.

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
