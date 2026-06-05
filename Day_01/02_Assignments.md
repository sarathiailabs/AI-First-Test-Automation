# Day 1: JavaScript Fundamentals – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts taught in the JavaScript Fundamentals session. Please attempt all questions and implement the coding assignments.

---

## Theory Questions

### Question 1
Explain the difference between declaring a variable with `const` and `let`. Write a short code snippet showing what happens if you attempt to reassign a variable declared with `const`.

### Question 2
List the five common primitive data types in JavaScript. Briefly explain how primitive data types and non-primitive data types (such as Objects or Arrays) differ in how they are stored in computer memory.

### Question 3
What is implicit type coercion in JavaScript? Explain why it is considered best practice to use strict equality (`===`) instead of loose equality (`==`) when writing automated assertions.

### Question 4
How does a `while` loop differ from a `for` loop? Describe a scenario in automation testing where a `while` loop is more appropriate than a `for` loop.

### Question 5
What is the difference between a function's **parameters** and its **arguments**? What value is returned by default if a function does not have a `return` statement?

---

## Practical Assignments

### Assignment 1: Student Marks Calculator
* **Difficulty Level:** Beginner
* **Concepts Covered:** Variables, Data Types, Arithmetic & Comparison Operators, Conditional Statements.

#### Problem Statement
Write a grading script that calculates a student's average marks and assigns a corresponding letter grade based on defined thresholds. This is representative of code that evaluates test scores or run summaries.

#### Requirements
1. Declare a constant variable to store the student's name (e.g., `"Alice Smith"`).
2. Declare three variables to store scores for HTML, CSS, and JavaScript as numbers (e.g., `85`, `90`, `92`).
3. Calculate the average score of the student using arithmetic operators.
4. Using an `if-else if-else` statement, determine the grade using the following criteria:
   * Average score >= 90: Grade A
   * Average score >= 80 and < 90: Grade B
   * Average score >= 70 and < 80: Grade C
   * Average score < 70: Grade F
5. Log a formatted report directly to the console displaying the student's name, individual subject marks, the computed average score, and the assigned letter grade.

#### Expected Output
```text
Student Name: Alice Smith
HTML Marks: 85
CSS Marks: 90
JS Marks: 92
Average Score: 89
Grade Assigned: Grade B
```

#### Hints
* The average score is computed by adding the three marks together and dividing the result by `3`. Use parentheses to control the order of calculations.
* Use comparison operators (`>=`, `<`) and logical operators (`&&`) in your conditional expressions.

---

### Assignment 2: Largest Number from Array
* **Difficulty Level:** Beginner-Intermediate
* **Concepts Covered:** Arrays, Loops, Conditional Statements.

#### Problem Statement
Write a script that scans an array of numbers representing test execution times (in seconds) and identifies the longest duration.

#### Requirements
1. Declare an array named `durations` containing at least five numbers representing execution durations: `120`, `450`, `310`, `600`, `250`.
2. Initialize a variable (e.g., `maxDuration`) to store the current largest number. Set its initial value to the first element in the array.
3. Use a standard `for` loop to iterate through the array, starting the loop index from `1` (since the first element is already captured).
4. Inside the loop, write a conditional statement to check if the current element is greater than `maxDuration`. If it is, update the value of `maxDuration` with the current element.
5. Print both the original array and the calculated largest duration to the console in a clear, reader-friendly format.

#### Expected Output
```text
Test Durations: [ 120, 450, 310, 600, 250 ]
The largest test duration is: 600 seconds.
```

#### Hints
* Retrieve the total size of the array using the `length` property of the array.
* Access elements in the array using bracket notation (e.g., `durations[i]`).

---

### Assignment 3: Employee Object CRUD
* **Difficulty Level:** Beginner-Intermediate
* **Concepts Covered:** Objects, Key-Value Pairs, Dot & Bracket Notation, Object Mutation.

#### Problem Statement
Write a program that initializes an employee record object and modifies its properties using CRUD (Create, Read, Update, Delete) concepts.

#### Requirements
1. **Create:** Create an object named `employee` containing the following property keys and values:
   * `id`: `101`
   * `name`: `"John Doe"`
   * `role`: `"QA Engineer"`
   * `department`: `"Quality Assurance"`
2. **Read:** Access and print the employee's `name` and `role` using dot notation.
3. **Update:** Modify the employee's `role` to `"Senior QA Engineer"` and dynamically add a new property `status` with the value `"Active"`.
4. **Delete:** Remove the `department` property from the `employee` object.
5. **Output Verification:** Log the final `employee` object to the console to verify all changes were successfully completed.

#### Expected Output
```text
Employee Name: John Doe, Role: QA Engineer
Updating employee role and status...
Deleting department...
Final Employee Object: { id: 101, name: 'John Doe', role: 'Senior QA Engineer', status: 'Active' }
```

#### Hints
* To add or update properties, use direct assignment (e.g., `object.property = newValue`).
* Use the `delete` keyword followed by the object and property reference (e.g., `delete object.property`) to remove a key.
