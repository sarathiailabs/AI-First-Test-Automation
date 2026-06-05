# Day 1: JavaScript Fundamentals – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and programming assignments provided for Day 1.

---

## Theory Question Solutions

### Question 1
Explain the difference between declaring a variable with `const` and `let`. Write a short code snippet showing what happens if you attempt to reassign a variable declared with `const`.

### Answer
* **`const` (Constant):** Declares variables whose values cannot be reassigned after initialization. They are block-scoped and must be assigned a value immediately when declared.
* **`let`:** Declares variables that can be reassigned. They are also block-scoped but do not require immediate assignment upon declaration.

#### Code Example:
```javascript
const appVersion = "1.0.0";
let testStatus = "Passed";

testStatus = "Failed"; // Permitted: let variables can be reassigned.
appVersion = "2.0.0";  // Throws TypeError: Assignment to constant variable.
```

---

### Question 2
List the five common primitive data types in JavaScript. Briefly explain how primitive data types and non-primitive data types (such as Objects or Arrays) differ in how they are stored in computer memory.

### Answer
The five common primitive data types in JavaScript are:
1. **String** (textual values)
2. **Number** (integers and floating-point numbers)
3. **Boolean** (`true` or `false`)
4. **Undefined** (uninitialized variable state)
5. **Null** (explicit absence of value)

#### Memory Storage Differences:
* **Primitive types** are stored directly in **Stack memory** by value. Their size is fixed in memory, and copying a primitive variable creates a completely independent copy of the value.
* **Non-primitive types** (Objects and Arrays) are stored in **Heap memory** by reference. Because their size can change dynamically, the variable on the stack stores only a memory address (reference) that points to the actual data stored in the Heap. Copying a reference variable copies only the address, meaning both variables point to the same object in memory.

---

### Question 3
What is implicit type coercion in JavaScript? Explain why it is considered best practice to use strict equality (`===`) instead of loose equality (`==`) when writing automated assertions.

### Answer
**Implicit type coercion** is the automatic conversion of a value from one data type to another by the JavaScript engine during operations (such as comparing a string and a number).

#### Why strict equality (`===`) is best practice:
* **Loose Equality (`==`)** automatically converts operand data types to match before comparing. For example, `1 == "1"` evaluates to `true` because the string `"1"` is coerced into the number `1`. This can mask bugs or lead to false positives in test assertions.
* **Strict Equality (`===`)** compares both the **value** and the **data type** without any coercion. `1 === "1"` evaluates to `false` because a number and a string are not of the same type. Using `===` ensures your assertions are exact and prevents unintended type conversion bypasses.

---

### Question 4
How does a `while` loop differ from a `for` loop? Describe a scenario in automation testing where a `while` loop is more appropriate than a `for` loop.

### Answer
* **`for` Loop:** Structured for counter-based iteration. It defines initialization, condition, and increment expression on a single line, making it ideal when you know the exact number of iterations beforehand (e.g., looping through the elements of an array).
* **`while` Loop:** Iterates based solely on a condition block. It repeats as long as the condition remains true, making it ideal when the number of iterations is variable or unknown at runtime.

#### Automation Testing Scenario:
A `while` loop is more appropriate when waiting for a page element to load or a status to change. For example, checking if a loading spinner is visible:
```javascript
let spinnerVisible = true;
let attempts = 0;

while (spinnerVisible && attempts < 10) {
    spinnerVisible = checkSpinnerState(); // Returns true or false
    wait(1000); // Wait 1 second before retrying
    attempts++;
}
```
Here, you cannot use a `for` loop easily because the spinner could disappear in 2 seconds or 8 seconds, making the exit condition dynamic.

---

### Question 5
What is the difference between a function's **parameters** and its **arguments**? What value is returned by default if a function does not have a `return` statement?

### Answer
* **Parameters** are the variable names listed in the function's declaration definition. They act as placeholders (e.g., `x` and `y` in `function add(x, y) {}`).
* **Arguments** are the actual values passed to the function when it is executed (e.g., `5` and `10` in `add(5, 10)`).
* If a function does not contain a `return` statement, it returns the value **`undefined`** by default when executed.

---

## Programming Assignment Solutions

### Assignment 1: Student Marks Calculator

#### Solution Code
```javascript
// Student Marks Calculator

// 1. Declare the student's name
const studentName = "Alice Smith";

// 2. Declare subject marks
const htmlMarks = 85;
const cssMarks = 90;
const jsMarks = 92;

// 3. Calculate the average score
// Parentheses ensure addition happens first before division
const averageScore = (htmlMarks + cssMarks + jsMarks) / 3;

// 4. Determine the grade using conditional statements
let grade;

if (averageScore >= 90) {
    grade = "Grade A";
} else if (averageScore >= 80 && averageScore < 90) {
    grade = "Grade B";
} else if (averageScore >= 70 && averageScore < 80) {
    grade = "Grade C";
} else {
    grade = "Grade F";
}

// 5. Print the formatted report to the console
console.log("Student Name: " + studentName);
console.log("HTML Marks: " + htmlMarks);
console.log("CSS Marks: " + cssMarks);
console.log("JS Marks: " + jsMarks);
console.log("Average Score: " + averageScore);
console.log("Grade Assigned: " + grade);
```

#### Explanation
* **Variables:** We define the student name as `const` because it doesn't change. Individual marks are declared as numeric variables.
* **Calculation:** The average is computed using `(htmlMarks + cssMarks + jsMarks) / 3`. The brackets are critical to ensure that addition executes first, adhering to operator precedence rules.
* **Conditionals:** An `else if` ladder is used. Since the conditions are evaluated sequentially, `averageScore >= 80 && averageScore < 90` successfully isolates averages in the 80s to assign Grade B.
* **Output:** Values are joined with labels using string concatenation (`+`) and output via `console.log`.

#### Output
```text
Student Name: Alice Smith
HTML Marks: 85
CSS Marks: 90
JS Marks: 92
Average Score: 89
Grade Assigned: Grade B
```

---

### Assignment 2: Largest Number from Array

#### Solution Code
```javascript
// Largest Number from Array

// 1. Declare test durations array
const durations = [120, 450, 310, 600, 250];

// 2. Initialize maxDuration with the first element of the array
let maxDuration = durations[0];

// 3. Iterate through the array starting from the second element (index 1)
for (let i = 1; i < durations.length; i++) {
    // 4. If current element is larger than current max, update maxDuration
    if (durations[i] > maxDuration) {
        maxDuration = durations[i];
    }
}

// 5. Output the results
console.log("Test Durations: [ " + durations.join(", ") + " ]");
console.log("The largest test duration is: " + maxDuration + " seconds.");
```

#### Explanation
* **Initialization:** We initialize `maxDuration` to `durations[0]` (`120`). This establishes a baseline for comparison.
* **Loop Iteration:** The `for` loop begins at index `1` and iterates while `i < durations.length` (up to index `4`).
* **Comparison Logic:** In each iteration, `durations[i]` is compared against `maxDuration`. 
  * At `i = 1`: `durations[1]` is `450`, which is `> 120`. `maxDuration` becomes `450`.
  * At `i = 2`: `310` is not `> 450`. No change.
  * At `i = 3`: `600` is `> 450`. `maxDuration` becomes `600`.
  * At `i = 4`: `250` is not `> 600`. No change.
* **Output:** Prints the original array using `.join(", ")` for clean formatting, followed by the calculated maximum.

#### Output
```text
Test Durations: [ 120, 450, 310, 600, 250 ]
The largest test duration is: 600 seconds.
```

---

### Assignment 3: Employee Object CRUD

#### Solution Code
```javascript
// Employee Object CRUD

// 1. CREATE: Initialize employee object
let employee = {
    id: 101,
    name: "John Doe",
    role: "QA Engineer",
    department: "Quality Assurance"
};

// 2. READ: Access and print properties using dot notation
console.log("Employee Name: " + employee.name + ", Role: " + employee.role);

// 3. UPDATE: Modify existing property and add new property
console.log("Updating employee role and status...");
employee.role = "Senior QA Engineer"; // Modifies existing property
employee.status = "Active";            // Dynamic property insertion

// 4. DELETE: Remove the department property
console.log("Deleting department...");
delete employee.department;

// 5. Output Verification
console.log("Final Employee Object:", employee);
```

#### Explanation
* **Create:** An object named `employee` is created using curly braces `{}` containing four key-value properties.
* **Read:** Dot notation is used to fetch values. Writing `employee.name` retrieves `"John Doe"` and `employee.role` retrieves `"QA Engineer"`.
* **Update:** We overwrite `role` by assigning a new string value. We add `status` by writing `employee.status = "Active"`; since the `status` key does not exist yet, JavaScript inserts it.
* **Delete:** The `delete` operator is applied to `employee.department`, which deletes the key-value pair completely.
* **Verification:** The entire `employee` object is printed, demonstrating the structural updates.

#### Output
```text
Employee Name: John Doe, Role: QA Engineer
Updating employee role and status...
Deleting department...
Final Employee Object: { id: 101, name: 'John Doe', role: 'Senior QA Engineer', status: 'Active' }
```
