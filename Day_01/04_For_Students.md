# Day 1: JavaScript Fundamentals – Student Revision Notes

This revision document is designed to help you quickly review the key concepts taught during the Day 1 lecture session.

---

## Variables

### Definition
A variable is a named container in computer memory used to store data values that can be referenced, used, or modified throughout a program.

### Important Syntax
```javascript
const constantName = value; // Variable that cannot be reassigned
let variableName = value;   // Variable that can be reassigned
```

### Example
```javascript
const maxTimeout = 30000;
let loginAttempts = 0;

// Reassigning a let variable
loginAttempts = 1;

// The line below would throw an error because maxTimeout is a constant:
// maxTimeout = 50000;
```

### Key Points
- Use `const` by default for variables that will not be reassigned.
- Use `let` for variables whose values are expected to change during program execution (like counters or toggle states).
- Avoid using the legacy `var` keyword, as it does not respect block scoping.

### Common Mistakes
- **Attempting to reassign a `const` variable:** Will throw a `TypeError: Assignment to constant variable.`
- **Declaring a `const` variable without an initial value:** Constants must be initialized with a value at the time of declaration.

---

## Data Types

### Definition
A data type is a classification of data that tells the JavaScript engine how the programmer intends to use the value.

### Important Syntax
```javascript
// Using typeof to inspect the data type of a value or variable
let typeString = typeof "Playwright"; // Returns "string"
```

### Example
```javascript
let appUrl = "https://example.com"; // String
let retryLimit = 3;                 // Number
let isVisible = true;               // Boolean
let selectedElement = null;         // Null (represents intentional empty value)
let pageTitle;                      // Undefined (variable declared but not initialized)
```

### Key Points
- **Primitive Data Types:** Stored directly in Stack memory by value. These are immutable (String, Number, Boolean, Undefined, Null).
- **Non-Primitive Data Types:** Stored in Heap memory by reference. These are mutable (Objects, Arrays).
- `typeof null` returns `"object"` due to a legacy bug in JavaScript, but it is treated as a primitive value.

### Common Mistakes
- **Confusing `null` and `undefined`:** `undefined` is the default state of an uninitialized variable, whereas `null` is an explicit assignment by the developer indicating "no value".
- **Implicit string coercion during addition:** Adding a number to a string (e.g., `"10" + 5`) results in string concatenation (`"105"`) instead of arithmetic addition.

---

## Operators

### Definition
Operators are special symbols used to perform calculations, assignments, comparisons, and logical checks on variables and values.

### Important Syntax
```javascript
// Arithmetic: +, -, *, /, %, **
// Assignment: =, +=, -=
// Comparison (Strict): ===, !==, >, <, >=, <=
// Logical: && (AND), || (OR), ! (NOT)
```

### Example
```javascript
let operandA = 10;
let operandB = 5;

let sum = operandA + operandB;            // Arithmetic: 15
let isSumCorrect = (sum === 15);          // Comparison: true
let shouldProceed = isSumCorrect && true; // Logical: true
```

### Key Points
- Always use strict equality (`===`) and strict inequality (`!==`) because they compare both value and data type without implicit conversion.
- Avoid loose equality (`==`) which automatically converts types and can mask bugs.

### Common Mistakes
- **Using a single assignment operator (`=`) instead of a strict comparison operator (`===`) inside conditionals:** Writing `if (status = "passed")` assigns the value instead of comparing it.

---

## Conditional Statements

### Definition
Conditional statements are control flow structures that execute specific blocks of code depending on whether a given condition evaluates to `true` or `false`.

### Important Syntax
```javascript
if (condition) {
    // Executes if condition is true
} else if (anotherCondition) {
    // Executes if anotherCondition is true
} else {
    // Executes if none of the above are true
}
```

### Example
```javascript
let currentStatus = "failed";

if (currentStatus === "passed") {
    console.log("Test Passed!");
} else if (currentStatus === "failed") {
    console.log("Test Failed!");
} else {
    console.log("Status Unknown");
}
```

### Key Points
- Use `if-else if-else` ladders to check range-based or dynamic conditions.
- Switch statements (`switch`) can be used to compare a single variable against multiple static cases for cleaner code layout.

### Common Mistakes
- **Forgetting the `break` statement in a `switch` block:** This causes the code execution to "fall through" and run the subsequent case blocks regardless of whether they match.

---

## Loops

### Definition
Loops are control structures used to repeatedly execute a block of code as long as a specified condition remains true.

### Important Syntax
```javascript
// For loop structure
for (initialization; condition; increment) {
    // Code block
}

// While loop structure
while (condition) {
    // Code block
}
```

### Example
```javascript
// Loop to count from 1 to 3
for (let i = 1; i <= 3; i++) {
    console.log("Count: " + i);
}
```

### Key Points
- Use a `for` loop when you know the exact number of iterations beforehand (e.g., repeating an action exactly 5 times).
- Use a `while` loop when the number of repetitions is dynamic or unknown (e.g., retrying an action until a condition is met).

### Common Mistakes
- **Creating an infinite loop:** Forgetting to update the variable that controls the loop condition inside the loop body (e.g., forgetting to decrement a counter), causing the loop to run forever.

---

## Functions

### Definition
A function is a self-contained, reusable block of code designed to perform a specific task, executed only when invoked (called).

### Important Syntax
```javascript
function functionName(parameter1, parameter2) {
    // Code logic
    return value; // Optional: returns value to the caller
}
```

### Example
```javascript
function calculateBill(price, tax) {
    let total = price + (price * tax);
    return total;
}

let finalAmount = calculateBill(100, 0.08); // Returns 108
```

### Key Points
- **Parameters** act as placeholder variables in the function declaration. **Arguments** are the actual values passed to the function when calling it.
- A function stops executing immediately when it encounters the `return` statement.

### Common Mistakes
- **Forgetting the `return` statement:** If a function is designed to output a result but lacks a `return` statement, it will return `undefined` by default.

---

## Arrays

### Definition
An array is an ordered, zero-indexed collection of values, allowing you to store multiple items under a single variable name.

### Important Syntax
```javascript
let arrayName = [item1, item2, item3];
let arrayLength = arrayName.length; // Property to get number of items
arrayName.push(newItem);            // Adds item to end of array
let lastItem = arrayName.pop();     // Removes item from end of array
```

### Example
```javascript
let browserList = ["chromium", "firefox"];

browserList.push("webkit"); // Adds webkit to the end
console.log(browserList[0]); // Accesses the first element: "chromium"
console.log(browserList.length); // Accesses the array length: 3
```

### Key Points
- Arrays are zero-indexed, meaning the first element is at index `0` and the last element is at index `length - 1`.
- Access and modify elements using their numerical indexes in brackets (e.g., `browserList[1]`).

### Common Mistakes
- **Off-by-one errors when accessing elements:** Attempting to retrieve the last element using `array[array.length]`. Since array indexes start at `0`, the last element is always at index `array.length - 1`.

---

## Objects

### Definition
An object is an unordered collection of related properties represented as `key: value` pairs.

### Important Syntax
```javascript
let objectName = {
    key1: value1,
    key2: value2
};

// Accessing properties
let value = objectName.key1;       // Dot notation
let valueAlt = objectName["key2"]; // Bracket notation
delete objectName.key1;            // Deleting a property
```

### Example
```javascript
let testReport = {
    testId: 101,
    status: "Passed"
};

testReport.duration = 45; // Adds a new property dynamically
testReport.status = "Failed"; // Updates an existing property
```

### Key Points
- Retrieve properties using dot notation (`object.property`) for readability.
- Bracket notation (`object["property"]`) is required if the key name contains spaces or is stored inside a variable.

### Common Mistakes
- **Omiting quotes in bracket notation:** Writing `object[key]` instead of `object["key"]`. JavaScript will look for a variable named `key` rather than accessing the property named `"key"`.

---

## Assignment Summary

- **Student Marks Calculator**
  - *Concepts Practiced:* Variables (`const` and `let`), basic arithmetic operations, conditional logic using `if-else if-else` ladders, and console logging.
- **Largest Number from Array**
  - *Concepts Practiced:* Array declaration, element indexing, iteration using a `for` loop, condition checks, and tracking state variables.
- **Employee Object CRUD**
  - *Concepts Practiced:* Object creation, reading properties via dot notation, updating existing property values, adding properties dynamically, and deleting properties using the `delete` keyword.

---

## Quick Revision Sheet

- **Variables:** Default to `const`. Use `let` only for values that change. Avoid `var`.
- **Primitives vs Non-Primitives:** String, Number, Boolean, Undefined, Null are stored by value. Objects and Arrays are stored by reference.
- **Equality:** Always check equality using `===` (strict check) to avoid type coercion bugs.
- **Control Flow:** Use loops to repeat blocks of code and conditionals to route execution paths.
- **Functions:** Wrap reusable code blocks in functions, using parameters for inputs and `return` for outputs.
- **Data Structures:** Use arrays (`[]`) for ordered lists and objects (`{}`) for key-value property maps.

---

## Important Takeaways

1. JavaScript is dynamic, but comparing values strictly using `===` is critical to prevent silent type errors in automated testing.
2. Correct variable declaration using block-scoped keywords (`const` and `let`) isolates data and stops variables from leaking outside their intended blocks.
3. Modular code relies on functions that do one thing and return predictable outputs.
4. Arrays and objects form the structural core of variables when dealing with real-world collections and API response structures.
