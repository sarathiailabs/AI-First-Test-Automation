# Day 1: JavaScript Fundamentals – Student Revision Notes

This revision document is designed to help you quickly review the key concepts taught during the Day 1 lecture session.

---

## Variables

### Definition
A **variable** is a named container in memory used to store data that a program can manipulate during execution. *(Ek naam wala dabba jo data store karta hai.)*

### Important Syntax
```javascript
const constantName = value; // Variable that cannot be reassigned (sealed container)
let variableName = value;   // Variable that can be reassigned (open container)
```

### Example
```javascript
const collegeName = "VJTI Mumbai";
let semesterFee = 45000;

// Reassigning a let variable (Fee increased next semester)
semesterFee = 48000;

// The line below would throw an error because collegeName is a constant:
// collegeName = "IIT Bombay";
```

### Key Points
- Use `const` by default for variables that will not be reassigned (e.g., college name, API base URL).
- Use `let` for variables whose values are expected to change during program execution (like a counter or fee total).
- Avoid using the legacy `var` keyword, as it does not respect block scoping and can cause unexpected bugs.

### Common Mistakes
- **Attempting to reassign a `const` variable:** Will throw a `TypeError: Assignment to constant variable.`
- **Declaring a `const` variable without initializing it:** Constants must be initialized with a value at the time of declaration.

---

## Data Types

### Definition
A **data type** is a classification of data that tells the JavaScript engine how the programmer intends to use the value. *(Data ka type jo computer ko batata hai ki iske sath kya operations karne hain.)*

### Important Syntax
```javascript
// Using typeof to inspect the data type of a value or variable
let typeString = typeof "Playwright"; // Returns "string"
```

### Example
```javascript
let studentName = "Priya Sharma";   // String (Text)
let marksMaths = 85;               // Number (Integer/Decimal)
let hasPaidFee = true;             // Boolean (True/False)
let scholarshipDetails = null;     // Null (Explicitly empty - no scholarship yet)
let hostelRoomNumber;              // Undefined (Declared but no room allocated yet)
```

### Key Points
- **Primitive Data Types:** Stored directly in Stack memory by value. These are immutable (String, Number, Boolean, Undefined, Null).
- **Non-Primitive Data Types:** Stored in Heap memory by reference. These are mutable (Objects, Arrays).
- `typeof null` returns `"object"` due to a legacy bug in JavaScript, but it is treated as a primitive value.

### Common Mistakes
- **Confusing `null` and `undefined`:** `undefined` is the default state of an uninitialized variable, whereas `null` is an explicit assignment by the developer indicating "no value" or "empty".
- **Implicit string coercion during addition:** Adding a number to a string (e.g., `"10" + 5`) results in string concatenation (`"105"`) instead of arithmetic addition.

---

## Operators

### Definition
**Operators** are special symbols used to perform calculations, assignments, comparisons, and logical checks on variables and values. *(Symbols jo data par calculation ya comparison karne me help karte hain.)*

### Important Syntax
```javascript
// Arithmetic: +, -, *, /, %, **
// Assignment: =, +=, -=
// Comparison (Strict): ===, !==, >, <, >=, <=
// Logical: && (AND), || (OR), ! (NOT)
```

### Example
```javascript
let messBill = 3500;
let gstRate = 0.05;

let totalBill = messBill + (messBill * gstRate); // Arithmetic: 3675
let isBillDiscounted = (totalBill > 4000);       // Comparison: false
let shouldRemindStudent = !isBillDiscounted;     // Logical: true
```

### Key Points
- Always use strict equality (`===`) and strict inequality (`!==`) because they compare both value and data type without implicit conversion.
- Avoid loose equality (`==`) which automatically converts types and can mask bugs (e.g., `5 == "5"` is true, which is dangerous in tests).

### Common Mistakes
- **Using a single assignment operator (`=`) instead of a strict comparison operator (`===`) inside conditionals:** Writing `if (hasPaid = true)` assigns the value instead of comparing it.

---

## Conditional Statements

### Definition
**Conditional statements** are control flow structures that execute specific blocks of code depending on whether a given condition evaluates to `true` or `false`. *(Faisla lene ke liye use hone wale code blocks.)*

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
let transactionStatus = "failed";

if (transactionStatus === "success") {
    console.log("Admission fee paid successfully!");
} else if (transactionStatus === "failed") {
    console.log("Transaction failed. Please try again.");
} else {
    console.log("Payment status pending verification.");
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
**Loops** are control structures used to repeatedly execute a block of code as long as a specified condition remains true. *(Ek hi kaam ko baar-baar repeat karne ka tarika.)*

### Important Syntax
```javascript
// For loop structure (use when count is known)
for (initialization; condition; increment) {
    // Code block
}

// While loop structure (use when count is unknown/dynamic)
while (condition) {
    // Code block
}
```

### Example
```javascript
// Printing count of hostel rooms from 101 to 103
for (let room = 101; room <= 103; room++) {
    console.log("Checking Room: " + room);
}
```

### Key Points
- Use a `for` loop when you know the exact number of iterations beforehand (e.g., repeating an action exactly 5 times).
- Use a `while` loop when the number of repetitions is dynamic or unknown (e.g., retrying an action until a condition is met).

### Common Mistakes
- **Creating an infinite loop:** Forgetting to update the variable that controls the loop condition inside the loop body (e.g., forgetting to increment a counter), causing the loop to run forever.

---

## Functions

### Definition
A **function** is a self-contained, reusable block of code designed to perform a specific task, executed only when invoked (called). *(Ek machine ya block jo bar-bar use ho sake.)*

### Important Syntax
```javascript
function functionName(parameter1, parameter2) {
    // Code logic
    return value; // Returns value to the caller
}
```

### Example
```javascript
// Function representing an ATM cash withdrawal machine
function withdrawCash(balance, amount) {
    if (amount <= balance) {
        return balance - amount;
    }
    return "Insufficient balance";
}

let newBalance = withdrawCash(5000, 2000); // returns 3000
```

### Key Points
- **Parameters** act as placeholder variables in the function declaration. **Arguments** are the actual values passed to the function when calling it.
- A function stops executing immediately when it encounters the `return` statement.

### Common Mistakes
- **Forgetting the `return` statement:** If a function is designed to output a result but lacks a `return` statement, it will return `undefined` by default.

---

## Arrays

### Definition
An **array** is an ordered, zero-indexed collection of values, allowing you to store multiple items under a single variable name. *(Items ki ek serial list, jaise train ke coaches.)*

### Important Syntax
```javascript
let arrayName = [item1, item2, item3];
let arrayLength = arrayName.length; // Property to get number of items
arrayName.push(newItem);            // Adds item to end of array
let lastItem = arrayName.pop();     // Removes item from end of array
```

### Example
```javascript
let studentRollNumbers = [101, 102, 103];

studentRollNumbers.push(104); // Adds 104 to the end
console.log(studentRollNumbers[0]); // Accesses the first element: 101
console.log(studentRollNumbers.length); // Accesses the array length: 4
```

### Key Points
- Arrays are zero-indexed, meaning the first element is at index `0` and the last element is at index `length - 1`.
- Access and modify elements using their numerical indexes in brackets (e.g., `studentRollNumbers[1]`).

### Common Mistakes
- **Off-by-one errors when accessing elements:** Attempting to retrieve the last element using `array[array.length]`. Since array indexes start at `0`, the last element is always at index `array.length - 1`.

---

## Objects

### Definition
An **object** is an unordered collection of related properties represented as `key: value` pairs. *(Kisi cheez ki complete details store karne wala format, jaise Aadhar card.)*

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
// Student details in college ERP database
let studentProfile = {
    name: "Rahul Verma",
    rollNo: 42,
    branch: "CS"
};

studentProfile.email = "rahul@vjti.ac.in"; // Adds a new property dynamically
studentProfile.branch = "IT"; // Updates an existing property
```

### Key Points
- Retrieve properties using dot notation (`object.property`) for readability.
- Bracket notation (`object["property"]`) is required if the key name contains spaces or is stored inside a variable.

### Common Mistakes
- **Omitting quotes in bracket notation:** Writing `object[key]` instead of `object["key"]`. JavaScript will look for a variable named `key` rather than accessing the property named `"key"`.

---

## Assignment Summary

- **Student Semester Marks Calculator**
  - *Concepts Practiced:* Variables (`const` and `let`), basic arithmetic operations, conditional logic using `if-else if-else` ladders, and console logging.
- **Largest Number from Array**
  - *Concepts Practiced:* Array declaration, element indexing, iteration using a `for` loop, condition checks, and tracking state variables (hostel mess bills).
- **Student Profile Object CRUD**
  - *Concepts Practiced:* Object creation, reading properties via dot notation, updating existing property values, adding properties dynamically, and deleting properties using the `delete` keyword.

---

## Quick Revision Sheet

- **Variables:** Default to `const`. Use `let` only for values that change (e.g., fee payments). Avoid `var`.
- **Primitives vs Non-Primitives:** String, Number, Boolean, Undefined, Null are stored by value. Objects and Arrays are stored by reference.
- **Equality:** Always check equality using `===` (strict check) to avoid type coercion bugs in test assertions.
- **Control Flow:** Use loops to repeat blocks of code (auto-rickshaw meter) and conditionals to route execution paths (ticket checker).
- **Functions:** Wrap reusable code blocks in functions, using parameters for inputs and `return` for outputs (ATM cash withdrawal).
- **Data Structures:** Use arrays (`[]`) for ordered lists (train coaches) and objects (`{}`) for key-value property maps (Aadhar card).

---

## Important Takeaways

1. JavaScript is dynamic, but comparing values strictly using `===` is critical to prevent silent type errors in automated testing.
2. Correct variable declaration using block-scoped keywords (`const` and `let`) isolates data and stops variables from leaking outside their intended blocks.
3. Modular code relies on functions that do one thing and return predictable outputs.
4. Arrays and objects form the structural core of variables when dealing with real-world collections and API response structures.
