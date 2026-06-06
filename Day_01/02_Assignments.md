# Day 1: JavaScript Fundamentals – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts taught in the JavaScript Fundamentals session and prepare you for technical interviews.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the difference between `let`, `const`, and `var` in JavaScript? When would you use each one? Write a short code snippet showing what happens if you attempt to reassign a variable declared with `const`.

### Question 2 🔥 **Frequently Asked**
What is the difference between `==` (loose equality) and `===` (strict equality) in JavaScript? Why is `===` recommended in production code and automated test assertions?

### Question 3 📌 **Important**
What is the difference between `null` and `undefined` in JavaScript? What does `typeof null` return, and why is that considered a JavaScript quirk?

### Question 4 📌 **Important**
What are the differences between primitive and non-primitive data types in JavaScript? How do they differ in terms of memory storage (Stack vs Heap)?

### Question 5 💡 **Good to Know**
What is the difference between a function's **parameters** and its **arguments**? What value is returned by default if a function does not have a `return` statement?

---

## Practical Assignments

### Assignment 1: Student Semester Marks Calculator

* **Interview Relevance:** Tests your ability to use variables, arithmetic operations, and conditional logic — the most basic skills verified in any fresher-level JavaScript coding round.
* **Difficulty Level:** Beginner
* **Concepts Covered:** Variables, Data Types, Arithmetic & Comparison Operators, Conditional Statements.

#### Problem Statement
Write a grading script that calculates a student's average semester marks and assigns a corresponding letter grade based on defined thresholds. This is the kind of small coding task you might be asked to solve in a technical interview within 10 minutes.

#### Requirements
1. Declare a constant variable to store the student's name (e.g., `"Priya Sharma"`).
2. Declare three variables to store scores for Maths, Physics, and Chemistry as numbers (e.g., `85`, `78`, `92`).
3. Calculate the average score of the student using arithmetic operators.
4. Using an `if-else if-else` statement, determine the grade using the following criteria:
   * Average score >= 90: Grade A
   * Average score >= 80 and < 90: Grade B
   * Average score >= 70 and < 80: Grade C
   * Average score < 70: Grade F
5. Log a formatted report to the console displaying the student's name, individual subject marks, the computed average score, and the assigned letter grade.

#### Expected Output
```text
Student Name: Priya Sharma
Maths Marks: 85
Physics Marks: 78
Chemistry Marks: 92
Average Score: 85
Grade Assigned: Grade B
```

#### Hints
* The average score is computed by adding the three marks together and dividing the result by `3`. Use parentheses to control the order of calculations.
* Use comparison operators (`>=`, `<`) and logical operators (`&&`) in your conditional expressions.

---

### Assignment 2: Largest Number from Array

* **Interview Relevance:** Finding the maximum/minimum from an array is one of the most common fresher-level interview questions — tests your loop and comparison logic.
* **Difficulty Level:** Beginner-Intermediate
* **Concepts Covered:** Arrays, Loops, Conditional Statements.

#### Problem Statement
Write a script that scans an array of numbers representing monthly hostel mess bills (in ₹) and identifies the highest bill amount.

#### Requirements
1. Declare an array named `messBills` containing at least five numbers representing monthly mess charges: `2800`, `3200`, `2950`, `3500`, `3100`.
2. Initialize a variable (e.g., `highestBill`) to store the current largest number. Set its initial value to the first element in the array.
3. Use a standard `for` loop to iterate through the array, starting the loop index from `1` (since the first element is already captured).
4. Inside the loop, write a conditional statement to check if the current element is greater than `highestBill`. If it is, update the value.
5. Print both the original array and the calculated largest bill to the console.

#### Expected Output
```text
Monthly Mess Bills: [ 2800, 3200, 2950, 3500, 3100 ]
The highest mess bill is: ₹3500
```

#### Hints
* Retrieve the total size of the array using the `length` property.
* Access elements in the array using bracket notation (e.g., `messBills[i]`).

---

### Assignment 3: Student Profile Object CRUD

* **Interview Relevance:** Object creation, modification, and property access using dot/bracket notation is tested in almost every JavaScript interview — this mirrors a real interview take-home task.
* **Difficulty Level:** Beginner-Intermediate
* **Concepts Covered:** Objects, Key-Value Pairs, Dot & Bracket Notation, Object Mutation.

#### Problem Statement
Write a program that initializes a student profile object and modifies its properties using CRUD (Create, Read, Update, Delete) concepts — similar to how student records are managed in a college ERP system.

#### Requirements
1. **Create:** Create an object named `student` containing the following property keys and values:
   * `rollNumber`: `42`
   * `name`: `"Amit Kumar"`
   * `branch`: `"Computer Science"`
   * `semester`: `5`
2. **Read:** Access and print the student's `name` and `branch` using dot notation.
3. **Update:** Modify the student's `semester` to `6` and dynamically add a new property `hostel` with the value `"Boys Hostel A"`.
4. **Delete:** Remove the `branch` property from the `student` object.
5. **Output Verification:** Log the final `student` object to the console to verify all changes were successfully completed.

#### Expected Output
```text
Student Name: Amit Kumar, Branch: Computer Science
Updating semester and adding hostel...
Deleting branch...
Final Student Object: { rollNumber: 42, name: 'Amit Kumar', semester: 6, hostel: 'Boys Hostel A' }
```

#### Hints
* To add or update properties, use direct assignment (e.g., `object.property = newValue`).
* Use the `delete` keyword followed by the object and property reference (e.g., `delete object.property`) to remove a key.
