# Day 1: JavaScript Fundamentals – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and programming assignments provided for Day 1.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is the difference between `let`, `const`, and `var` in JavaScript? When would you use each one? Write a short code snippet showing what happens if you attempt to reassign a variable declared with `const`.

### Answer
| Feature | `const` | `let` | `var` |
| --- | --- | --- | --- |
| Re-assignable | No | Yes | Yes |
| Re-declarable | No | No | Yes |
| Scope | Block | Block | Function/Global |

* **`const` (Constant):** Declares variables whose values cannot be reassigned after initialization. They are block-scoped and must be assigned a value immediately when declared. Use `const` by default for values that don't change (e.g., college name, API URL).
* **`let`:** Declares variables that can be reassigned. They are also block-scoped. Use `let` for values that change (e.g., loop counters, student attendance count).
* **`var`:** The legacy way to declare variables. It is function-scoped and can be re-declared, leading to potential bugs. Avoid in modern code.

#### Code Example:
```javascript
const collegeName = "VJTI Mumbai";
let semesterFee = 45000;

semesterFee = 48000; // Permitted: let variables can be reassigned.
// collegeName = "IIT Bombay";  // Throws TypeError: Assignment to constant variable.
```

---

### Question 2 🔥 **Frequently Asked**
What is the difference between `==` (loose equality) and `===` (strict equality) in JavaScript? Why is `===` recommended in production code and automated test assertions?

### Answer
* **Loose Equality (`==`)** automatically converts operand data types to match before comparing. For example, `1 == "1"` evaluates to `true` because the string `"1"` is coerced into the number `1`. This can mask bugs or lead to false positives in test assertions.
* **Strict Equality (`===`)** compares both the **value** and the **data type** without any coercion. `1 === "1"` evaluates to `false` because a number and a string are not of the same type.

#### Why `===` is recommended:
In automated testing (Playwright, Selenium), using `===` ensures your assertions are exact. For example, if a Flipkart product count API returns `"10"` (string) instead of `10` (number), loose equality would pass silently, but strict equality catches the mismatch — which could be a real bug.

```javascript
console.log(5 == "5");   // true (type coercion)
console.log(5 === "5");  // false (no coercion — recommended)
```

---

### Question 3 📌 **Important**
What is the difference between `null` and `undefined` in JavaScript? What does `typeof null` return, and why is that considered a JavaScript quirk?

### Answer
* **`undefined`** is the default state when a variable has been declared but not initialized. JavaScript assigns it automatically.
  ```javascript
  let hostelRoom;         // hostelRoom is undefined (no room allotted yet)
  ```
* **`null`** is an intentional value explicitly assigned by the developer to represent "no value" or "empty."
  ```javascript
  let pendingFee = null;  // Explicitly set to mean "no pending fee"
  ```

#### `typeof null` quirk:
`typeof null` returns `"object"` — this is a well-known legacy bug in JavaScript since its first version. It has never been fixed because changing it would break millions of existing programs. Despite this, `null` is treated as a primitive value.

---

### Question 4 📌 **Important**
What are the differences between primitive and non-primitive data types in JavaScript? How do they differ in terms of memory storage (Stack vs Heap)?

### Answer
| Aspect | Primitive Types | Non-Primitive Types |
| --- | --- | --- |
| Types | String, Number, Boolean, Undefined, Null | Object, Array |
| Storage | Stack memory (by value) | Heap memory (by reference) |
| Size | Fixed | Dynamic (can grow/shrink) |
| Copying | Creates independent copy | Copies reference (both point to same data) |
| Mutability | Immutable | Mutable |

*(Stack memory mein value seedha store hoti hai. Heap memory mein data store hota hai aur Stack mein sirf uska address hota hai — jaise hostel room ka address hostel register mein likha hota hai.)*

---

### Question 5 💡 **Good to Know**
What is the difference between a function's **parameters** and its **arguments**? What value is returned by default if a function does not have a `return` statement?

### Answer
* **Parameters** are the variable names listed in the function's declaration. They act as placeholders (e.g., `amount` and `rate` in `function calculateGST(amount, rate) {}`).
* **Arguments** are the actual values passed to the function when it is executed (e.g., `3500` and `5` in `calculateGST(3500, 5)`).
* If a function does not contain a `return` statement, it returns the value **`undefined`** by default.

```javascript
function greet(studentName) {    // studentName is the parameter
    return "Hello, " + studentName;
}

greet("Priya");                  // "Priya" is the argument
```

---

## Programming Assignment Solutions

### Assignment 1: Student Semester Marks Calculator

#### Solution Code
```javascript
// Student Semester Marks Calculator

// 1. Declare the student's name
const studentName = "Priya Sharma";

// 2. Declare subject marks
const mathsMarks = 85;
const physicsMarks = 78;
const chemistryMarks = 92;

// 3. Calculate the average score
// Parentheses ensure addition happens first before division
const averageScore = (mathsMarks + physicsMarks + chemistryMarks) / 3;

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
console.log("Maths Marks: " + mathsMarks);
console.log("Physics Marks: " + physicsMarks);
console.log("Chemistry Marks: " + chemistryMarks);
console.log("Average Score: " + averageScore);
console.log("Grade Assigned: " + grade);
```

#### Explanation
* **Variables:** We define the student name as `const` because it doesn't change. Individual marks are declared as numeric variables.
* **Calculation:** The average is computed using `(mathsMarks + physicsMarks + chemistryMarks) / 3`. The brackets are critical to ensure that addition executes first, adhering to operator precedence rules.
* **Conditionals:** An `else if` ladder is used. Since the conditions are evaluated sequentially, `averageScore >= 80 && averageScore < 90` successfully isolates averages in the 80s to assign Grade B.
* **Output:** Values are joined with labels using string concatenation (`+`) and output via `console.log`.

#### Output
```text
Student Name: Priya Sharma
Maths Marks: 85
Physics Marks: 78
Chemistry Marks: 92
Average Score: 85
Grade Assigned: Grade B
```

---

### Assignment 2: Largest Number from Array

#### Solution Code
```javascript
// Largest Number from Array — Highest Hostel Mess Bill

// 1. Declare mess bills array
const messBills = [2800, 3200, 2950, 3500, 3100];

// 2. Initialize highestBill with the first element of the array
let highestBill = messBills[0];

// 3. Iterate through the array starting from the second element (index 1)
for (let i = 1; i < messBills.length; i++) {
    // 4. If current element is larger than current max, update highestBill
    if (messBills[i] > highestBill) {
        highestBill = messBills[i];
    }
}

// 5. Output the results
console.log("Monthly Mess Bills: [ " + messBills.join(", ") + " ]");
console.log("The highest mess bill is: ₹" + highestBill);
```

#### Explanation
* **Initialization:** We initialize `highestBill` to `messBills[0]` (`2800`). This establishes a baseline for comparison.
* **Loop Iteration:** The `for` loop begins at index `1` and iterates while `i < messBills.length` (up to index `4`).
* **Comparison Logic:** In each iteration, `messBills[i]` is compared against `highestBill`.
  * At `i = 1`: `messBills[1]` is `3200`, which is `> 2800`. `highestBill` becomes `3200`.
  * At `i = 2`: `2950` is not `> 3200`. No change.
  * At `i = 3`: `3500` is `> 3200`. `highestBill` becomes `3500`.
  * At `i = 4`: `3100` is not `> 3500`. No change.
* **Output:** Prints the original array using `.join(", ")` for clean formatting, followed by the calculated maximum.

#### Output
```text
Monthly Mess Bills: [ 2800, 3200, 2950, 3500, 3100 ]
The highest mess bill is: ₹3500
```

---

### Assignment 3: Student Profile Object CRUD

#### Solution Code
```javascript
// Student Profile Object CRUD

// 1. CREATE: Initialize student profile object
let student = {
    rollNumber: 42,
    name: "Amit Kumar",
    branch: "Computer Science",
    semester: 5
};

// 2. READ: Access and print properties using dot notation
console.log("Student Name: " + student.name + ", Branch: " + student.branch);

// 3. UPDATE: Modify existing property and add new property
console.log("Updating semester and adding hostel...");
student.semester = 6;                   // Modifies existing property
student.hostel = "Boys Hostel A";       // Dynamic property insertion

// 4. DELETE: Remove the branch property
console.log("Deleting branch...");
delete student.branch;

// 5. Output Verification
console.log("Final Student Object:", student);
```

#### Explanation
* **Create:** An object named `student` is created using curly braces `{}` containing four key-value properties — like fields on a college ID card.
* **Read:** Dot notation is used to fetch values. Writing `student.name` retrieves `"Amit Kumar"` and `student.branch` retrieves `"Computer Science"`.
* **Update:** We overwrite `semester` by assigning `6`. We add `hostel` by writing `student.hostel = "Boys Hostel A"`; since the `hostel` key does not exist yet, JavaScript inserts it dynamically.
* **Delete:** The `delete` operator is applied to `student.branch`, which removes the key-value pair completely from the object.
* **Verification:** The entire `student` object is printed, demonstrating the structural updates.

#### Output
```text
Student Name: Amit Kumar, Branch: Computer Science
Updating semester and adding hostel...
Deleting branch...
Final Student Object: { rollNumber: 42, name: 'Amit Kumar', semester: 6, hostel: 'Boys Hostel A' }
```
