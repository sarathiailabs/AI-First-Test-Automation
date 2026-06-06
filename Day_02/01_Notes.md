# Day 2: Modern JavaScript and OOP

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| let & const | 5 mins |
| Arrow Functions | 5 mins |
| Template Literals | 5 mins |
| Destructuring | 10 mins |
| Spread Operator | 5 mins |
| Rest Operator | 5 mins |
| Optional Chaining | 5 mins |
| map() | 10 mins |
| filter() | 10 mins |
| reduce() | 10 mins |
| Scope | 10 mins |
| Closures | 10 mins |
| Classes & Objects | 10 mins |
| Encapsulation | 5 mins |
| Inheritance | 5 mins |
| Polymorphism | 5 mins |
| Abstraction | 10 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Choose correctly between `let` and `const` based on scope and reassignment needs.
* Write concise arrow functions and handle lexical `this` contexts.
* Use destructuring, template literals, optional chaining, and spread/rest operators for cleaner code.
* Leverage map, filter, and reduce to process collections without manual loops.
* Explain lexical scope, block scope, and closure execution flows.
* Model test scenarios using Classes, Inheritance, Encapsulation, Polymorphism, and Abstraction.

---

## Introduction

Modern JavaScript (ES6+) transformed how developers write clean, maintainable, and readable code. For test automation engineers using tools like Playwright, learning modern syntax (like destructuring, arrow functions, and higher-order array methods) is essential. It enables you to clean up test setups, parse complex configuration files, and interact with web components efficiently. 

Additionally, Object-Oriented Programming (OOP) concepts are the building blocks of automation framework designs, such as the Page Object Model (POM). Mastering OOP principles like encapsulation and inheritance allows you to create reusable browser action libraries and construct modular, scalable test suites.

---

## let & const

### Definition
`let` and `const` are block-scoped variable declaration keywords introduced in ES6, replacing the legacy function-scoped `var`. *(Block-scoped declaration keywords jo var ki jagah use hote hain.)*

### Key Concepts
* **Block Scope:** Any variable declared with `let` or `const` inside curly braces `{}` is only accessible within that block.
* **Reassignment:**
  * `let` allows you to change the value stored in the variable.
  * `const` creates a read-only reference. You cannot reassign the variable. However, non-primitive values (like arrays and objects) declared with `const` can have their internal properties mutated.
* **Temporal Dead Zone (TDZ):** Variables declared with `let` or `const` are hoisted but not initialized. Accessing them before declaration throws a `ReferenceError`.

### Syntax
```javascript
let upiTransactionCount = 1;
const collegeName = "VJTI Mumbai";

// Objects/Arrays declared with const can mutate, but cannot be reassigned:
const studentProfile = { name: "Rahul", branch: "CS" };
studentProfile.branch = "IT"; // Allowed!
// studentProfile = {};       // TypeError!
```

### Visual Explanation
Variables are like labeled kitchen jars:
```text
Hostel Room Shelf (Global Memory)
└─ [Lockable Box] ──► const collegeName = "VJTI Mumbai" (Immutable value)
└─ { Private Locker (Block Scope) } ──► let pocketMoney = 2000; (Only inside locker)
```

### Example
#### Code
```javascript
let upiTransactionStatus = "pending";
const collegeCode = "VJTI-MUM";

if (true) {
    let upiTransactionStatus = "success"; // Shadowing inside block
    console.log("Inside block:", upiTransactionStatus); 
}

console.log("Outside block:", upiTransactionStatus);
// collegeCode = "IIT-B"; // Uncaught TypeError: Assignment to constant variable.
```
#### Output
```text
Inside block: success
Outside block: pending
```
#### Explanation
1. `upiTransactionStatus` is defined as `"pending"` globally.
2. Inside the block, a new block-scoped variable `upiTransactionStatus` is declared with `let`, temporarily shadowing the outer one.
3. Outside the block, the original `upiTransactionStatus` remains unchanged.

### Real World Usage
In Playwright, configurations and element pointers are declared as `const`, while counters, dynamically fetched titles, or retries are declared as `let`:
```javascript
const testUrl = "https://vjti.ac.in";
let currentRetryCount = 0;
```

### Common Mistakes
* **Attempting to reassign a `const` primitive:** Throws a `TypeError`. Use `let` if the value needs to change.
* **Assuming `const` makes objects completely immutable:** It does not. Use `Object.freeze()` if you want to prevent object property changes.

### Topic Summary
Use `const` by default. Only use `let` if the variable needs to be reassigned. Both respect block-scoping `{}`.

---

## Arrow Functions

### Definition
An **arrow function** is a concise way to write function expressions in JavaScript using the `=>` syntax. *(Function likhne ka chota aur aasan tarika jo lexical 'this' use karta hai.)*

### Key Concepts
* **Concise Syntax:** Omits the `function` keyword. If the function contains only a single return expression, curly braces `{}` and the `return` keyword can be omitted.
* **Implicit Return:** Single-line arrow functions return the evaluated expression automatically.
* **Lexical `this`:** Unlike regular functions, arrow functions do not have their own `this` context. They inherit `this` from the enclosing execution scope.

### Syntax
```javascript
// Single parameter (no parentheses needed), implicit return
const doubleAmount = x => x * 2;

// Multiple parameters, explicit return
const calculateBill = (price, tax) => {
    return price + tax;
};
```

### Visual Explanation
```text
Regular Function: [Parent Scope] ──► [Function (creates its own 'this' context)]
Arrow Function:   [Parent Scope ('this')] ──► [Arrow Function (uses parent's 'this' directly)]
```

### Example
#### Code
```javascript
const calculateDeliveryFee = (distance, rate) => distance * rate;
console.log("Delivery Fee (₹):", calculateDeliveryFee(5, 12));

const studentSession = {
    collegeName: "VJTI Mumbai",
    startTimer: function() {
        // Arrow function inherits 'this' from startTimer()
        setTimeout(() => {
            console.log("College inside timeout:", this.collegeName);
        }, 100);
    }
};
studentSession.startTimer();
```
#### Output
```text
Delivery Fee (₹): 60
College inside timeout: VJTI Mumbai
```
#### Explanation
1. `calculateDeliveryFee` is a single-line arrow function that calculates and implicitly returns the delivery fee.
2. Inside `studentSession.startTimer`, the arrow function inside `setTimeout` inherits the lexical `this` from the parent method `startTimer` (which refers to `studentSession`).

### Real World Usage
Often used as callbacks in test assertions and array operations in Playwright:
```javascript
const premiumStudents = students.filter(student => student.feePaid === true);
```

### Common Mistakes
* **Using arrow functions as object methods:** Since they don't have their own `this`, writing an object method as an arrow function will cause `this` to point to the global/window object.
* **Forgetting curly braces require an explicit `return`:** `x => { x * 2 }` returns `undefined`. You must write `x => { return x * 2; }` or `x => x * 2`.

### Topic Summary
Arrow functions offer a short syntax. They lack their own `this` binding, making them ideal for callbacks and array processing, but not for object methods.

---

## Template Literals

### Definition
**Template literals** are string literals delimited with backticks (`` ` ``), allowing embedded expressions and multi-line strings. *(Backticks use karke dynamic string aur variable print karne ka smart tarika.)*

### Key Concepts
* **String Interpolation:** Insert variables or expressions directly into strings using `${expression}`.
* **Multi-line Strings:** Write strings across multiple lines without using escape characters like `\n`.
* **Expression Evaluation:** Any valid JavaScript expression (math, function calls) can be put inside `${}`.

### Syntax
```javascript
const greeting = `Welcome, ${studentName}!`;
const receipt = `Item 1: ₹${price1}
Item 2: ₹${price2}`;
```

### Visual Explanation
```text
"Student " + name + " has roll number " + roll + "!"  ◄── Legacy concatenation
`Student ${name} has roll number ${roll}!`            ◄── Template literal interpolation
```

### Example
#### Code
```javascript
const studentName = "Rahul Verma";
const rollNo = 101;
const semesterMarks = [80, 85, 90];

const reportCard = `[COLLEGE REPORT]
Student Name: ${studentName}
Roll Number:  ${rollNo}
Average Marks: ${semesterMarks.reduce((sum, val) => sum + val, 0) / semesterMarks.length}`;

console.log(reportCard);
```
#### Output
```text
[COLLEGE REPORT]
Student Name: Rahul Verma
Roll Number:  101
Average Marks: 85
```
#### Explanation
The template literal resolves the variables and runs the calculation inside the `${}` delimiters directly, preserving all spacing and newlines.

### Real World Usage
Generating dynamic selectors or log messages in automated tests:
```javascript
const studentRowSelector = `//tr[@id='student-${rollNo}']/td`;
```

### Common Mistakes
* **Confusing single quotes (`'`) or double quotes (`"`) with backticks (`` ` ``):** String interpolation `${}` will print literally as text in single/double quotes.
* **Overcomplicating code inside `${}`:** Keep expressions short; call a helper function instead if the logic inside the interpolation becomes complex.

### Topic Summary
Template literals use backticks to enable clean variable interpolation and multi-line text representations.

---

## Destructuring

### Definition
**Destructuring** is a special syntax that unpacks values from arrays, or properties from objects, into distinct variables. *(Object ya Array me se variables ko direct nikalne ka simple syntax.)*

### Key Concepts
* **Object Destructuring:** Extract properties by matching their key names.
* **Array Destructuring:** Extract values sequentially by their positions (indices).
* **Default Values:** Assign fallback values in case the property or index is `undefined`.
* **Renaming:** Change the variable name during object destructuring.

### Syntax
```javascript
// Object Destructuring with renaming and default value
const { name: studentName, hostelRoom = 101 } = studentDetails;

// Array Destructuring
const [firstRoll, secondRoll] = rollList;
```

### Visual Explanation
```text
Object: { name: "Priya", age: 20 } ──► Destructuring ──► let { name, age } = obj;
                                                          (name = "Priya", age = 20)
```

### Example
#### Code
```javascript
const courseConfig = {
    courseName: "Playwright Testing",
    durationDays: 30,
    fees: 15000
};

// Destructuring with renaming and default value
const { courseName, durationDays: duration, isOnline = true } = courseConfig;

console.log("Course Name:", courseName);
console.log("Duration:", duration);
console.log("Is Online:", isOnline);

const selectedRolls = [101, 102, 103];
const [primaryRoll, , backupRoll] = selectedRolls; // Skips second element

console.log("Primary Student:", primaryRoll);
console.log("Backup Student:", backupRoll);
```
#### Output
```text
Course Name: Playwright Testing
Duration: 30
Is Online: true
Primary Student: 101
Backup Student: 103
```
#### Explanation
1. Property `courseName` is extracted directly.
2. Property `durationDays` is extracted and renamed to `duration`.
3. Property `isOnline` does not exist in `courseConfig`, so it falls back to the default value `true`.
4. In the array example, comma skip syntax `, ,` is used to skip the second element.

### Real World Usage
Used in Playwright test parameter blocks and configuration objects:
```javascript
test('Verify student profile dashboard', async ({ page }) => {
    // Destructured 'page' from the runner's context
});
```

### Common Mistakes
* **Destructuring from `null` or `undefined`:** Throws a `TypeError`. Ensure the target object exists before destructuring.
* **Using wrong names in object destructuring:** Key names must match the object properties exactly (unless renamed with `:` syntax).

### Topic Summary
Destructuring breaks down objects and arrays into individual variables, making code cleaner and reducing manual variable assignments.

---

## Spread Operator

### Definition
The **spread operator** (`...`) expands an iterable (like an array or object) into individual elements or properties. *(Array ya Object ko expand karne ya merge karne ka tool.)*

### Key Concepts
* **Shallow Copying:** Create copies of arrays or objects without modifying the original (immutability).
* **Merging:** Combine multiple arrays or objects into one.
* **Function Arguments:** Pass array elements as separate arguments to a function.

### Syntax
```javascript
const copiedList = [...originalList];
const mergedDetails = { ...detailsA, ...detailsB };
```

### Visual Explanation
```text
[101, 102] and [103, 104] ──► [...arr1, ...arr2] ──► [101, 102, 103, 104]
```

### Example
#### Code
```javascript
const basicStudent = { name: "Priya", branch: "CS" };
const academicDetails = { branch: "IT", gpa: 9.2, college: "VJTI" };

// Merge objects (last property overrides matching previous keys)
const completeProfile = { ...basicStudent, ...academicDetails };
console.log("Complete Profile:", completeProfile);

const batchA = [101, 102];
const batchB = [103, 104];
const allStudents = [...batchA, 105, ...batchB];

console.log("All Roll Numbers:", allStudents);
```
#### Output
```text
Complete Profile: { name: 'Priya', branch: 'IT', gpa: 9.2, college: 'VJTI' }
All Roll Numbers: [ 101, 102, 105, 103, 104 ]
```
#### Explanation
1. `completeProfile` merges `basicStudent` and `academicDetails`. Since both have the `branch` property, the last one in order (`academicDetails.branch`) overwrites it.
2. `allStudents` combines two separate arrays and a single number element into a flat array using spread syntax.

### Real World Usage
Updating automation settings dynamically without mutating global configurations:
```javascript
const localConfig = { ...globalConfig, headless: false };
```

### Common Mistakes
* **Assuming deep copy:** The spread operator only performs a *shallow copy*. Nested objects or arrays will still be copied by reference.

### Topic Summary
The spread operator (`...`) expands objects and arrays. It is commonly used to duplicate, merge, or extend data structures safely.

---

## Rest Operator

### Definition
The **rest operator** (`...`) collects multiple remaining elements or arguments into a single array structure. *(Bache hue arguments ko ek array me ek sath collect karne ka tarika.)*

### Key Concepts
* **Variadic Functions:** Allow functions to accept an indefinite number of arguments.
* **Destructuring Remainder:** Capture the leftover properties of an object or elements of an array.
* **Location Rule:** The rest parameter must always be the last parameter in the function declaration or destructuring pattern.

### Syntax
```javascript
function collectRolls(firstRoll, ...otherRolls) {
    // otherRolls is an array
}

const [firstItem, ...remainingItems] = array;
```

### Visual Explanation
```text
Arguments: (101, 102, 103, 104) ──► function(first, ...others)
                                    first = 101, others = [102, 103, 104]
```

### Example
#### Code
```javascript
// Function with rest parameter
function printAbsentees(className, ...rollNumbers) {
    console.log(`Class: ${className}`);
    console.log(`Absent Count: ${rollNumbers.length}`);
    console.log(`Roll Numbers:`, rollNumbers);
}

printAbsentees("IT-Batch A", 45, 12, 89);

const studentMarks = [85, 90, 78, 92];
const [topperScore, runnerUpScore, ...otherScores] = studentMarks;

console.log("Topper Score:", topperScore);
console.log("Other Scores Array:", otherScores);
```
#### Output
```text
Class: IT-Batch A
Absent Count: 3
Roll Numbers: [ 45, 12, 89 ]
Topper Score: 85
Other Scores Array: [ 78, 92 ]
```
#### Explanation
1. `printAbsentees` takes the first argument as `className`. All other trailing arguments are gathered into the `rollNumbers` array.
2. In the array destructuring example, `otherScores` collects the remaining unassigned scores into a new array.

### Real World Usage
Writing generic wrapper functions or logging utilities in framework utilities:
```javascript
function logStep(stepName, ...metaDetails) {
    console.log(`[STEP] ${stepName}:`, metaDetails);
}
```

### Common Mistakes
* **Placing the rest parameter anywhere other than the end:** Writing `function test(...args, last) {}` throws a `SyntaxError`.

### Topic Summary
The rest operator (`...`) gathers multiple arguments or values into an array. It must be placed at the end of the argument list or destructuring pattern.

---

## Optional Chaining

### Definition
**Optional chaining** (`?.`) is an operator that permits reading the value of a property located deep within a chain of connected objects without validating that each reference in the chain is valid. *(Nested properties ko call karte waqt crash se bachane wala checking tool.)*

### Key Concepts
* **Short-circuiting:** If the object value before the `?.` is `null` or `undefined`, execution stops, and it returns `undefined` instead of throwing a crash error.
* **Function & Array Calls:** Can be applied to dynamically invoke optional functions or access dynamic indexes: `obj.method?.()` or `arr?.[0]`.

### Syntax
```javascript
const city = student?.profile?.address?.city;
```

### Visual Explanation
```text
Without Optional Chaining:
student && student.profile && student.profile.address && student.profile.address.city

With Optional Chaining:
student?.profile?.address?.city  (Returns undefined if any node is null/undefined)
```

### Example
#### Code
```javascript
const studentERPResponse = {
    status: 200,
    data: {
        students: [
            { name: "Rahul Verma", details: { hostelRoom: 105 } },
            { name: "Priya Sharma" } // Details missing (day scholar)
        ]
    }
};

const roomUser1 = studentERPResponse.data?.students?.[0]?.details?.hostelRoom;
const roomUser2 = studentERPResponse.data?.students?.[1]?.details?.hostelRoom; // details is undefined

console.log("User 1 Room:", roomUser1);
console.log("User 2 Room:", roomUser2);
```
#### Output
```text
User 1 Room: 105
User 2 Room: undefined
```
#### Explanation
When checking `details.hostelRoom` for User 2 (Priya), `details` is missing (`undefined`). Optional chaining (`details?.hostelRoom`) short-circuits at that level, returning `undefined` instead of throwing a `TypeError: Cannot read properties of undefined`.

### Real World Usage
Parsing optional settings or handling dynamic JSON payloads returned from backend APIs:
```javascript
const userCity = apiResponse?.data?.user?.address?.city?.trim();
```

### Common Mistakes
* **Using `?.` when you expect a property to exist:** Optional chaining hides silent bugs. If a field is mandatory, avoid using `?.` so the code fails explicitly.
* **Using `?.` to assign values:** `obj?.prop = "value"` is invalid and throws a syntax error.

### Topic Summary
Optional chaining (`?.`) prevents crashes when accessing deeply nested object properties by returning `undefined` if any node is empty.

---

## map()

### Definition
The **`map()`** method creates a new array populated with the results of calling a provided callback function on every element in the calling array. *(Array ke har element ko badal kar ek naya equal-sized array banane ka method.)*

### Key Concepts
* **Immutability:** Returns a brand new array. The original array remains unchanged.
* **1-to-1 Mapping:** The output array always has the exact same length as the source array.

### Syntax
```javascript
const newArray = array.map((element, index, arr) => {
    return newValue;
});
```

### Visual Explanation
```text
Input Array:  [ ₹100,  ₹200,  ₹300 ]
                 │      │      │   (x => x + 10 GST)
Output Array: [ ₹110,  ₹210,  ₹310 ]
```

### Example
#### Code
```javascript
const itemsInCart = [
    { name: "Notebook", price: 120 },
    { name: "Scientific Calculator", price: 850 }
];

// Apply 10% discount on prices for a college promotion
const discountedPrices = itemsInCart.map(item => item.price * 0.9);

console.log("Original Items:", itemsInCart);
console.log("Discounted Prices (₹):", discountedPrices);
```
#### Output
```text
Original Items: [ { name: 'Notebook', price: 120 }, { name: 'Scientific Calculator', price: 850 } ]
Discounted Prices (₹): [ 108, 765 ]
```
#### Explanation
`map()` iterates through the array. For each item, it executes `item.price * 0.9`, calculates the discounted price, and appends it to the newly returned `discountedPrices` array.

### Real World Usage
Extracting text lists from web page locator arrays or parsing raw API pricing lists:
```javascript
const cleanPrices = rawPricesList.map(priceText => Number(priceText.replace("₹", "")));
```

### Common Mistakes
* **Using `map()` when you do not need the returned array:** If you just want to iterate and perform side effects (like logs or database writes), use `forEach()` instead.
* **Forgetting to return a value inside the map callback:** If you don't return anything, the output array will be filled with `undefined` values.

### Topic Summary
`map()` processes every element in an array and returns a new array of matching length containing the transformed elements.

---

## filter()

### Definition
The **`filter()`** method creates a shallow copy of a portion of a given array, filtered down to just the elements that pass the logical condition implemented by the provided callback function. *(Condition lagakar array me se sirf matching items ko filter karne ka method.)*

### Key Concepts
* **Boolean Condition:** The callback function must return a truthy/falsy value. If truthy, the item is kept; if falsy, it is skipped.
* **Immutability:** Returns a new array. The original array remains unchanged.

### Syntax
```javascript
const filteredArray = array.filter((element, index) => {
    return condition; // true or false
});
```

### Visual Explanation
```text
Input Array:      [ 45, 88, 35, 92, 28 ]
                    │   │   │   │   │  (x >= 35) (Pass Threshold)
Filtered Array:   [ 45, 88, 35, 92 ]
```

### Example
#### Code
```javascript
const studentRecords = [
    { name: "Rahul Verma", score: 85, feePaid: true },
    { name: "Priya Sharma", score: 92, feePaid: false },
    { name: "Amit Patel", score: 70, feePaid: true }
];

// Filter only students who have paid fees
const eligibleStudents = studentRecords.filter(student => student.feePaid === true);

console.log("Eligible for Exams:", eligibleStudents);
```
#### Output
```text
Eligible for Exams: [
  { name: 'Rahul Verma', score: 85, feePaid: true },
  { name: 'Amit Patel', score: 70, feePaid: true }
]
```
#### Explanation
`filter()` runs `student.feePaid === true` for each record. It evaluates to `true` for Rahul and Amit, returning a new filtered array containing these two student objects.

### Real World Usage
Filtering failed test suites or searching table columns during automation:
```javascript
const failedRequests = transactionLogs.filter(log => log.status === "FAILED");
```

### Common Mistakes
* **Expecting `filter()` to find a single object:** `filter()` always returns an array, even if it matches only one item. Use `find()` if you want the first matching object itself.

### Topic Summary
`filter()` iterates through an array and returns a new, potentially shorter array containing only the items that meet a specific boolean condition.

---

## reduce()

### Definition
The **`reduce()`** method executes a user-supplied "reducer" callback function on each element of the array, in order, passing in the return value from the calculation on the preceding element. The final result of running the reducer across all elements of the array is a single value. *(Array ke saare elements ko mila kar ek single value banane ka method.)*

### Key Concepts
* **Accumulator:** The variable that stores the accumulated result of the previous iterations.
* **Initial Value:** An optional starting value for the accumulator. If omitted, the first element of the array is used as the initial value, and the loop starts at index 1.
* **Versatility:** Can convert an array into a single number, string, object, or another array structure.

### Syntax
```javascript
const result = array.reduce((accumulator, currentValue, currentIndex) => {
    return nextAccumulatorValue;
}, initialValue);
```

### Visual Explanation
```text
Array: [ ₹120, ₹850 ]  Initial Accumulator: 0
Step 1: Accumulator = 0,   Current = 120 ──► Accumulator + Current = 120
Step 2: Accumulator = 120, Current = 850 ──► Accumulator + Current = 970 (Final Result)
```

### Example
#### Code
```javascript
const stationaryBill = [
    { name: "Notebook", price: 120 },
    { name: "Scientific Calculator", price: 850 },
    { name: "Pen Set", price: 100 }
];

// Sum the prices of all stationary items
const grandTotal = stationaryBill.reduce((total, item) => {
    return total + item.price;
}, 0); // Initial value of total is 0

console.log("Grand Total Bill (₹):", grandTotal);
```
#### Output
```text
Grand Total Bill (₹): 1070
```
#### Explanation
1. `reduce` starts with `total = 0`.
2. For each item in the array, it adds `item.price` to `total` and returns it.
3. The returned value becomes the `total` parameter for the next iteration.
4. After processing the final element, `1070` is returned.

### Real World Usage
Summing prices in a shopping cart assertion or consolidating complex tabular test data into a summary object:
```javascript
const totalTestTime = runs.reduce((sum, run) => sum + run.durationMs, 0);
```

### Common Mistakes
* **Forgetting the initial value for object arrays:** If you omit `0` in the example above, the initial value becomes the first object `{ name: "Notebook", price: 120 }` instead of a number, leading to NaN errors when trying to add prices to it.

### Topic Summary
`reduce()` consolidates an array of elements down to a single value by running an accumulator operation across all elements sequentially.

---

## Scope

### Definition
**Scope** refers to the current context of execution in which values and expressions are visible or can be referenced. *(Code me variables kahan dikhenge aur kahan access ho sakte hain uski boundary.)*

### Key Concepts
* **Global Scope:** Variables declared outside any function or block. They are accessible anywhere in the program.
* **Function Scope:** Variables declared inside a function (using `var`, `let`, or `const`). They can only be accessed within that function.
* **Block Scope:** Variables declared inside a `{}` block (using `let` or `const`). They are inaccessible outside the block.
* **Scope Chain:** The process by which JavaScript looks up variables. If it can't find a variable locally, it looks outward to parent scopes until it reaches the global scope.

### Syntax
```javascript
const globalVar = "Global";

function checkScope() {
    const functionVar = "Function";
    if (true) {
        const blockVar = "Block";
    }
}
```

### Visual Explanation
```text
┌──────────────────────────────────────────────┐
│ Global Scope (Accessible everywhere)         │
│  ┌────────────────────────────────────────┐  │
│  │ Function Scope (Inside function only)  │  │
│  │  ┌──────────────────────────────────┐  │  │
│  │  │ Block Scope (Inside {} block)    │  │  │
│  │  └──────────────────────────────────┘  │  │
│  └────────────────────────────────────────┘  │
└──────────────────────────────────────────────┘
```

### Example
#### Code
```javascript
const collegeName = "VJTI Mumbai";

function allocateHostel() {
    const wardenName = "Dr. K. Prasad";
    
    if (true) {
        const roomNumber = "Room 105";
        console.log("Inside block - College Name:", collegeName);
        console.log("Inside block - Warden Name:", wardenName);
        console.log("Inside block - Room Number:", roomNumber);
    }
    
    // console.log(roomNumber); // ReferenceError: roomNumber is not defined
}

allocateHostel();
```
#### Output
```text
Inside block - College Name: VJTI Mumbai
Inside block - Warden Name: Dr. K. Prasad
Inside block - Room Number: Room 105
```
#### Explanation
1. `collegeName` is accessible anywhere, including inside functions and blocks.
2. `wardenName` is function-scoped; it is accessible inside `allocateHostel()` and any blocks inside it.
3. `roomNumber` is block-scoped. Attempting to log it outside the `if` block triggers a `ReferenceError`.

### Real World Usage
Isolating variables inside test scripts or hooks to prevent collision of temporary variables (like loop indices or selectors) across test files:
```javascript
test('isolated test block', () => {
    const tempUrl = "https://vjti.ac.in"; // Block-scoped to this test only
});
```

### Common Mistakes
* **Leaking variables globally by forgetting declaration keywords:** Writing `x = 10` inside a block or function without `let` or `const` implicitly creates a global variable `x` (in non-strict mode), causing bugs.

### Topic Summary
Scope governs variable visibility. Global is accessible everywhere, Function is limited to functions, and Block is limited to curly braces `{}` (applicable to `let` and `const`).

---

## Closures

### Definition
A **closure** is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives an inner function access to the outer function's scope even after the outer function has returned. *(Inner function ka apne outer function ke variables ko yaad rakhna, outer function ke khatam hone ke baad bhi.)*

### Key Concepts
* **Lexical Environment Preservation:** When a function is created, it retains a reference to the environment in which it was declared.
* **State Encapsulation:** Closures can create private variables that cannot be modified directly from the outside.

### Syntax
```javascript
function outer() {
    let count = 0;
    return function inner() {
        count++;
        return count;
    };
}
const counter = outer();
```

### Visual Explanation
```text
[ outer() execution finished ] ──► (Variables usually cleared)
But inner() holds a reference to outer's variables!
[ inner() Closure Scope ] ──► Holds reference to: let count = 0;
```

### Example
#### Code
```javascript
function createTokenGenerator(studentName) {
    let tokenCount = 0;
    return function() {
        tokenCount++; // Accesses outer tokenCount variable
        return `${studentName.substring(0, 3).toUpperCase()}-${tokenCount}`;
    };
}

const rahulGenerator = createTokenGenerator("Rahul Verma");
const priyaGenerator = createTokenGenerator("Priya Sharma");

console.log(rahulGenerator());
console.log(rahulGenerator());
console.log(priyaGenerator()); // Maintains its own independent scope
console.log(rahulGenerator());
```
#### Output
```text
RAH-1
RAH-2
PRI-1
RAH-3
```
#### Explanation
1. `createTokenGenerator` returns an inner function that references `tokenCount` and `studentName`.
2. Even after `createTokenGenerator` finishes executing, the returned function retains access to `tokenCount` in memory.
3. `rahulGenerator` and `priyaGenerator` have separate closures, meaning they maintain independent `tokenCount` environments.

### Real World Usage
Creating dynamic test data generators or private configuration builders in test libraries:
```javascript
const getNextSequence = createTokenGenerator("RUN");
```

### Common Mistakes
* **Unintentional memory leaks:** Since closures keep references to outer variables in memory, storing excessively large data sets in closure scopes can lead to memory overhead.

### Topic Summary
A closure allows an inner function to remember and access variables from its outer lexical scope even after that outer function has finished executing.

---

## Classes

### Definition
A **class** is a blueprint/template introduced in ES6 for creating objects with shared properties and methods, acting as syntactical sugar over JavaScript's prototype-based inheritance. *(Naye objects banane ke liye ek structured blueprint ya design map.)*

### Key Concepts
* **Constructor:** A special method (`constructor`) used to initialize properties when an object is instantiated.
* **Instance Methods:** Functions defined in the class that are available on every object instance.
* **Static Methods:** Methods called directly on the class itself rather than on instances.

### Syntax
```javascript
class ClassName {
    constructor(param) {
        this.property = param;
    }
    method() {
        // Logic
    }
}
```

### Visual Explanation
```text
Class Blueprint (Hostel Room) ──► instantiate (new Room(105, 3)) ──► Object Instance
```

### Example
#### Code
```javascript
class HostelRoom {
    constructor(roomNo, capacity) {
        this.roomNo = roomNo;
        this.capacity = capacity;
    }
    
    checkIn() {
        console.log(`Checking in student to Room No: ${this.roomNo}`);
    }
    
    static getWardenContact() {
        return "+91-9876543210";
    }
}

// Instantiate objects
const room105 = new HostelRoom(105, 3);
room105.checkIn();

// Call static method directly on class
console.log("Warden Contact:", HostelRoom.getWardenContact());
```
#### Output
```text
Checking in student to Room No: 105
Warden Contact: +91-9876543210
```
#### Explanation
1. The `HostelRoom` class specifies a constructor that maps `roomNo` and `capacity` to the instance object.
2. The `checkIn` method can be called on any room instance (like `room105`).
3. The static method `getWardenContact` belongs to the class definition itself and is accessed using `HostelRoom.getWardenContact()`.

### Real World Usage
Representing Pages in the Page Object Model (POM) within testing frameworks:
```javascript
class LoginPage {
    constructor(page) {
        this.page = page;
        this.usernameInput = page.locator("#username");
    }
}
```

### Common Mistakes
* **Calling static methods on instance objects:** `room105.getWardenContact()` throws a `TypeError`. Static methods can only be called on the Class itself.
* **Forgetting the `new` keyword:** Calling `const rm = HostelRoom(105, 3)` throws a `TypeError: Class constructor HostelRoom cannot be invoked without 'new'`.

### Topic Summary
Classes define structure and behavior templates. Use `constructor` to set values on instantiation, and call static methods directly on the class name.

---

## Objects

### Definition
In Object-Oriented Programming, an **object** is an instance of a class that combines state (properties) and behavior (methods). *(Class se bani hui real entities jo variables (state) aur functions (behavior) ko combine karti hain.)*

### Key Concepts
* **Instantiation:** Creating an object instance from a class using the `new` keyword.
* **State:** The values assigned to the object's properties at any given time.
* **Behavior:** The methods the object can execute.

### Syntax
```javascript
const instance = new ClassName(arguments);
```

### Visual Explanation
```text
Class Blueprint: Student Profile
  - Name, Branch
  - printDetails()
       │
       ▼ (new Student("Rahul", "CS"))
Object Instance:
  - Name: "Rahul", Branch: "CS"
  - printDetails() ──► Executes print action
```

### Example
#### Code
```javascript
class CollegeStudent {
    constructor(name, rollNo) {
        this.name = name;
        this.rollNo = rollNo;
    }
    
    submitAssignment() {
        console.log(`Student ${this.name} (Roll No: ${this.rollNo}) has submitted the assignment.`);
    }
}

// Creating two distinct object instances from the same Class blueprint
const student1 = new CollegeStudent("Amit Patel", 45);
const student2 = new CollegeStudent("Priya Sharma", 12);

student1.submitAssignment();
student2.submitAssignment();
```
#### Output
```text
Student Amit Patel (Roll No: 45) has submitted the assignment.
Student Priya Sharma (Roll No: 12) has submitted the assignment.
```
#### Explanation
`student1` and `student2` are separate object instances. They share the same `submitAssignment` behavior template, but maintain distinct properties (`name` and `rollNo`).

### Real World Usage
Representing UI elements, components, or API endpoints in automated frameworks:
```javascript
const usernameInput = new Element("#username-field", "Input");
```

### Common Mistakes
* **Sharing object state unexpectedly:** Modifying the properties of one instance does not affect another. Ensure you initialize state inside the constructor so instances remain isolated.

### Topic Summary
Objects are individual, active instances of classes that store data and provide methods defined by their class blueprint.

---

## Encapsulation

### Definition
**Encapsulation** is the practice of bundling data (properties) and methods that operate on that data within a single unit, while restricting direct access to some of the object's components (using private variables). *(Data ko class ke andar chupa kar rakhna aur public methods ke bina access na hone dena.)*

### Key Concepts
* **Information Hiding:** Protecting an object's internal state from unintended external modification.
* **Private Fields:** Declared using the hash `#` prefix in modern JavaScript. Private fields are inaccessible from outside the class block.
* **Getters and Setters:** Public methods used to safely read or write private fields, allowing validation logic.

### Syntax
```javascript
class BankAccount {
    #balance = 0; // Private field

    get balance() {
        return this.#balance;
    }
}
```

### Visual Explanation
```text
┌─────────────────────────────────┐
│ Encapsulated Class (SBI Account) │
│  Private State: #balance ◄──────┼─── Access Blocked from outside
│  Public Methods:                │
│    - getBalance()               │
│    - deposit(amount) ───────────┼─── Public Access Allowed
└─────────────────────────────────┘
```

### Example
#### Code
```javascript
class SbiAccount {
    #balance = 0; // Private field

    constructor(accountHolder) {
        this.accountHolder = accountHolder;
    }

    // Getter to check balance
    get balance() {
        return this.#balance;
    }

    // Setter to update balance with safe validation
    deposit(amount) {
        if (amount > 0) {
            this.#balance += amount;
            console.log(`Deposited ₹${amount} successfully.`);
        } else {
            console.log("[Error] Deposit amount must be positive.");
        }
    }
}

const myAccount = new SbiAccount("Rahul Verma");
console.log("Initial Balance (₹):", myAccount.balance);

myAccount.deposit(5000); // Success
console.log("Updated Balance (₹):", myAccount.balance);

myAccount.deposit(-100); // Fails setter validation

// console.log(myAccount.#balance); // SyntaxError: Private field '#balance' must be declared in an enclosing class
```
#### Output
```text
Initial Balance (₹): 0
Deposited ₹5000 successfully.
Updated Balance (₹): 5000
[Error] Deposit amount must be positive.
```
#### Explanation
1. `#balance` is declared as a private field using `#`.
2. The getter `get balance()` allows safe external reads.
3. The method `deposit()` verifies that only positive amounts can be deposited. Attempting to deposit negative values prints an error.
4. Accessing `myAccount.#balance` directly triggers a syntax error.

### Real World Usage
Protecting sensitive automation credentials or state configurations (like environment variables) in framework libraries:
```javascript
class DatabaseConnector {
    #connectionString;
}
```

### Common Mistakes
* **Forgetting to declare private fields with `#` at the class top level:** In JavaScript, you cannot initialize private fields dynamically inside the constructor without declaring them at the class level first.
* **Bypassing encapsulation:** Creating public methods that return direct references to internal mutable data structures (like arrays), allowing external mutation.

### Topic Summary
Encapsulation groups data and methods while using private fields (`#`) along with getters/setters to protect internal states from unauthorized modifications.

---

## Inheritance

### Definition
**Inheritance** is a mechanism in which one class (the child/subclass) acquires the properties and methods of another class (the parent/superclass), promoting code reuse. *(Ek class (child) ka dusri class (parent) ke features aur functions ko reuse karna.)*

### Key Concepts
* **Extends:** The `extends` keyword is used to create a child class.
* **Super Constructor:** The child class constructor must invoke `super()` before accessing `this`. This runs the parent class constructor.
* **Method Overriding:** A child class can redefine a method from the parent class to implement specialized behavior.

### Syntax
```javascript
class ChildClass extends ParentClass {
    constructor(param) {
        super(param); // Runs parent constructor
    }
}
```

### Visual Explanation
```text
  Parent Class (BusPass) ──► Properties: holderName, Methods: printPass()
          ▲
          │ (extends)
  Child Class (StudentPass) ──► Inherits all, adds custom concession details
```

### Example
#### Code
```javascript
class GeneralBusPass {
    constructor(holderName, fee) {
        this.holderName = holderName;
        this.fee = fee;
    }

    printPass() {
        console.log(`Bus Pass Holder: ${this.holderName} | Fee Paid: ₹${this.fee}`);
    }
}

class StudentBusPass extends GeneralBusPass {
    constructor(holderName, collegeName) {
        super(holderName, 500); // Concession fee is fixed at ₹500 for students
        this.collegeName = collegeName;
    }

    verifyStudentStatus() {
        console.log(`Verifying enrollment at ${this.collegeName}... Status: Verified.`);
    }
}

const studentPass = new StudentBusPass("Amit Patel", "VJTI Mumbai");
studentPass.printPass(); // Inherited method
studentPass.verifyStudentStatus(); // Subclass method
```
#### Output
```text
Bus Pass Holder: Amit Patel | Fee Paid: ₹500
Verifying enrollment at VJTI Mumbai... Status: Verified.
```
#### Explanation
1. `StudentBusPass` extends `GeneralBusPass`, inheriting the `printPass` method.
2. The call `super(holderName, 500)` runs the constructor of `GeneralBusPass`, setting `holderName` and setting a fixed concessions fee.
3. An instance of `StudentBusPass` can run both inherited methods (`printPass`) and custom child methods (`verifyStudentStatus`).

### Real World Usage
Creating structured Page Objects where specialized pages inherit common methods (like click, navigate, verifyTitle) from a base Page class:
```javascript
class DashboardPage extends BasePage {}
```

### Common Mistakes
* **Forgetting to call `super()` in the subclass constructor:** Throws a `ReferenceError` when trying to access `this`.
* **Deep inheritance chains:** Overusing inheritance leads to hard-to-maintain code. Prefer composition (nesting objects) if the relationship is not strictly "is-a".

### Topic Summary
Inheritance lets a child class extend a parent class using the `extends` keyword, calling `super()` to run the parent constructor and reuse common behaviors.

---

## Polymorphism

### Definition
**Polymorphism** (Greek for "many forms") is the ability of different classes to respond to the same method call in their own unique way. *(Same method name par different classes ka apna-apna alag tareeke se kaam karna.)*

### Key Concepts
* **Method Overriding:** A child class overrides a method inherited from its parent. When the method is invoked, JavaScript runs the version corresponding to the specific object type.
* **Interface Uniformity:** Callers interact with a common interface without needing to know the specific subclass type of the target object.

### Syntax
```javascript
// Multiple classes define the same method name:
class Parent { perform() {} }
class ChildA extends Parent { perform() { /* override */ } }
class ChildB extends Parent { perform() { /* override */ } }
```

### Visual Explanation
```text
Call: paymentGateway.makePayment(1000)
  - If gateway is PaytmUPI ──► Process payment via mobile number/UPI ID
  - If gateway is CardPayment ──► Process payment via card PIN verification
```

### Example
#### Code
```javascript
class PaymentMethod {
    pay(amount) {
        console.log(`Processing general payment of ₹${amount}...`);
    }
}

class PaytmUPI extends PaymentMethod {
    pay(amount) {
        console.log(`Processing Paytm UPI payment of ₹${amount} using UPI PIN...`);
    }
}

class CardPayment extends PaymentMethod {
    pay(amount) {
        console.log(`Processing Card payment of ₹${amount} using OTP authentication...`);
    }
}

// Polymorphic executor
function completeTransaction(paymentMethod, billAmount) {
    paymentMethod.pay(billAmount); // Executes method dynamically based on class type
}

completeTransaction(new PaytmUPI(), 1500);
completeTransaction(new CardPayment(), 2500);
```
#### Output
```text
Processing Paytm UPI payment of ₹1500 using UPI PIN...
Processing Card payment of ₹2500 using OTP authentication...
```
#### Explanation
`PaytmUPI` and `CardPayment` override the `pay` method inherited from `PaymentMethod`. The `completeTransaction` helper executes `paymentMethod.pay(billAmount)` dynamically, yielding different logs depending on the type of payment instance passed.

### Real World Usage
Executing actions across different browser runners or handling test reporting across different formatters (e.g. HTML reports vs Console logs):
```javascript
reporters.forEach(reporter => reporter.generateReport());
```

### Common Mistakes
* **Mismatching method signatures:** If the overridden method has a different name or arguments, it breaks polymorphic consistency, leading to invocation errors.

### Topic Summary
Polymorphism allows child classes to implement unique behaviors for a shared method interface, executing the correct version dynamically at runtime.

---

## Abstraction

### Definition
**Abstraction** is the process of hiding the complex internal implementation details of an object and exposing only the essential features needed for interaction. *(Internal coding details ko chupa kar user ko sirf aasan aur important options dikhana.)*

### Key Concepts
* **Complexity Hiding:** Users of a class interact with a clean, high-level API without dealing with low-level setup code.
* **Abstract Classes (Simulated):** JavaScript doesn't natively support abstract classes. We simulate them by throwing an error in the parent class constructor or base methods to force child classes to implement them.

### Syntax
```javascript
class AbstractBase {
    constructor() {
        if (new.target === AbstractBase) {
            throw new Error("Cannot instantiate abstract class directly.");
        }
    }
}
```

### Visual Explanation
```text
User Interface: [Press Withdrawal Button on ATM]
                      │
                      ▼ (Abstract Interface)
Internal Mechanism: [Validate PIN, check database balance, trigger cash dispenser, update log]
```

### Example
#### Code
```javascript
class AtmMachine {
    constructor() {
        if (new.target === AtmMachine) {
            throw new Error("Cannot instantiate abstract ATM directly.");
        }
    }

    // Abstract method placeholder
    withdrawCash(amount) {
        throw new Error("Method 'withdrawCash()' must be implemented by subclass.");
    }
}

class SbiAtmMachine extends AtmMachine {
    withdrawCash(amount) {
        console.log(`Connecting securely to SBI network...`);
        console.log(`Checking balance...`);
        console.log(`Dispensing ₹${amount} cash. Transaction complete.`);
    }
}

try {
    const mainMachine = new AtmMachine(); // Error
} catch (e) {
    console.log("Error caught:", e.message);
}

const myAtm = new SbiAtmMachine();
myAtm.withdrawCash(5000);
```
#### Output
```text
Error caught: Cannot instantiate abstract ATM directly.
Connecting securely to SBI network...
Checking balance...
Dispensing ₹5000 cash. Transaction complete.
```
#### Explanation
1. `AtmMachine` blocks direct instantiation using `new.target`.
2. It defines an abstract method `withdrawCash` that throws an error to force child classes to override it.
3. `SbiAtmMachine` implements `withdrawCash` properly, allowing successful execution.

### Real World Usage
Exposing a simple `login` method to test scripts while hiding the complex element interaction, waiting, and validation steps:
```javascript
// Test script:
await loginPage.login("studentRoll45", "password123");
// Implementation details (locators, waiting, element clicks) are abstracted inside LoginPage.
```

### Common Mistakes
* **Exposing too many details:** Exposing internal locator properties or raw network configs directly in public APIs defeats the purpose of abstraction.

### Topic Summary
Abstraction hides inner complexities, exposing only clean APIs. It is simulated in JS by checking `new.target` and throwing error warnings in base class methods.

---

## Session Summary

### Key Takeaways
1. **Modern JS Syntax:** Use `const` by default for variables, destructure parameters, use template literals for strings, and leverage optional chaining to prevent runtime crashes.
2. **Dynamic Operations:** Spread (`...`) clones and merges data structure contents; Rest (`...`) bundles lists of inputs. `map`, `filter`, and `reduce` allow clean, loop-free array processing.
3. **Execution Context:** Scope establishes visibility boundaries. Closures allow functions to capture and reference their surrounding lexical context variables after return execution.
4. **OOP Structures:** Classes act as structural blueprints. Encapsulation hides state using private variables (`#`). Inheritance shares behaviors, Polymorphism handles dynamic overrides, and Abstraction hides complexity.

### Important Interview Points
* **How do you simulate abstract classes in JavaScript?**
  * Check `new.target === BaseClass` inside the base constructor and throw an error to block direct instantiation.
* **What is lexical scope?**
  * It is the scope resolved during compile time based on where functions are physically written in the code.
* **Explain the difference between Rest and Spread operators.**
  * Spread expands an array/object into separate items (e.g. copying). Rest aggregates separate items into an array structure (e.g. variadic arguments).
* **How do map, filter, and reduce differ?**
  * `map()` transforms each item 1-to-1. `filter()` removes items based on a check. `reduce()` consolidates elements into a single result.

### Quick Revision Sheet

* **Template Literals & Optional Chaining:**
  ```javascript
  const msg = `Logged: ${student?.name ?? "Guest"}`;
  ```
* **Array Methods:**
  ```javascript
  const activeIds = items.filter(i => i.active).map(i => i.id);
  ```
* **Classes & Private Variables:**
  ```javascript
  class Test {
      #runner = "Playwright";
      get runner() { return this.#runner; }
  }
  ```
* **Inheritance & Super:**
  ```javascript
  class SubTest extends Test {
      constructor() { super(); }
  }
  ```
