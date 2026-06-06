# Day 2: Modern JavaScript and OOP – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and programming assignments provided for Day 2.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is a **Closure** in JavaScript? Explain the lexical environment concept. How can closures help you build private configuration managers or test status trackers in an automation framework?

### Answer
A **closure** is the combination of a function bundled together with references to its surrounding state (its lexical environment). In JavaScript, closures are created every time a function is created, at function creation time. This allows an inner function to access variables defined in its outer function's scope even after the outer function has finished executing.

*(Inner function ka apne outer function ke variables ko yaad rakhna, outer function ke khatam hone ke baad bhi.)*

#### Real-World Test Automation Scenario:
In test automation, you often need to generate unique sequential roll numbers or transaction tokens during test setup. A closure can hide and maintain the incrementing state (a counter variable) so that it cannot be altered accidentally by other test scripts:
```javascript
function createRollNumberGenerator(prefix) {
    let count = 0; // Private state
    return function() {
        count++; // Accesses outer scope variable
        return `${prefix}-${count}`;
    };
}
const getNextRoll = createRollNumberGenerator("VJTI");
console.log(getNextRoll()); // VJTI-1
console.log(getNextRoll()); // VJTI-2
```

---

### Question 2 🔥 **Frequently Asked**
What is the difference between the **Spread operator (`...`)** and the **Rest operator (`...`)** in JavaScript? Provide short syntax snippets for both. Can they be used together in the same line of code?

### Answer
Both operators use the same three-dot syntax (`...`), but they perform opposite actions based on the context in which they are used:
* **Spread Operator:** Used to unpack or expand an array or object into individual elements or properties (e.g., copying or merging). *(Array ya Object ko open karna.)*
* **Rest Operator:** Used to pack or collect multiple separate values or arguments into a single array structure (e.g., function arguments or destructuring remainder). *(Separate items ko aggregate karke array banana.)*

They can be used in the same line of code, for example, when destructuring an array:
```javascript
// Spread: Merge two canteen menus
const snacks = ["Samosa", "Vada Pav"];
const drinks = ["Chai", "Lassi"];
const menu = [...snacks, ...drinks]; // ["Samosa", "Vada Pav", "Chai", "Lassi"]

// Rest: Gathers remaining scores
const [topper, ...remaining] = [95, 88, 76, 62]; 
// topper = 95, remaining = [88, 76, 62]
```

---

### Question 3 📌 **Important**
How do `map()`, `filter()`, and `reduce()` differ in terms of their return values and use cases? Write down the return type of each method. Why are they preferred over standard `for` loops in modern automation test assertions?

### Answer
* **`map()`**:
  * *Use Case:* Transforms every item in an array 1-to-1 (e.g., converting pricing numbers to a specific format).
  * *Return Value:* A **new array** of the exact same length as the original array.
* **`filter()`**:
  * *Use Case:* Selects a subset of elements that pass a boolean logic check (e.g., keeping only failed test runs).
  * *Return Value:* A **new array** containing only the matching elements (can be empty or shorter than the original).
* **`reduce()`**:
  * *Use Case:* Combines or aggregates all elements into a single accumulated result (e.g., summing total shopping cart bill).
  * *Return Value:* A **single value** (can be a number, string, object, array, etc.).

#### Why preferred over standard loops:
They promote declarative, functional code. Instead of writing boilerplate code (initializing empty arrays, managing loop indices, pushing items), these methods read like natural English, making automation code cleaner and less error-prone.

---

### Question 4 📌 **Important**
What is the purpose of **Encapsulation** in Object-Oriented Programming? How do you implement private fields in modern JavaScript classes (ES6+), and what error is thrown if you attempt to access them directly from outside the class?

### Answer
The purpose of **Encapsulation** is to restrict direct access to an object's internal state (data hiding) and bundle data with the methods that operate on that data. This protects the object from entering invalid states (like setting a negative balance or an invalid grade) due to unauthorized external modifications.

In modern JavaScript (ES2022+), you implement private fields by prefixing the variable name with the hash symbol (`#`). These fields must be declared at the top level of the class block.

If you attempt to access a private field directly (e.g., `student.#grade`), JavaScript throws a **`SyntaxError`** during execution compilation.

#### Example:
```javascript
class SbiAccount {
    #balance = 0; // Private field declaration
    constructor(initialAmount) {
        this.#balance = initialAmount;
    }
}
```

---

### Question 5 💡 **Good to Know**
How does **Polymorphism** differ from **Inheritance**? Explain how method overriding and polymorphic execution are used to implement common browser actions or multi-environment configuration loading.

### Answer
* **Inheritance** is when a subclass derives properties and behaviors from a parent class using the `extends` keyword. It represents an **"is-a"** relationship and focuses on code reuse.
* **Polymorphism** is the ability of different classes to implement the same method signature in their own unique way. It focuses on interface uniformity.

#### Reusable Verification Steps:
In an automation framework, you might have different payment pages (e.g., `PaytmUPI`, `CardPayment`, `NetBanking`). If all of these inherit from a base payment class and override a polymorphic method like `pay(amount)`, you can write a generic helper function `completeTransaction(paymentMethod, amount)` that processes the payment regardless of whether it's UPI or Card. This avoids writing separate scripts for each gateway.

---

## Programming Assignment Solutions

### Assignment 1: Student Management System (College ERP)

#### Solution Code
```javascript
// Student Management System (College ERP)

// 1. Create Base Class Person
class Person {
    constructor(name) {
        this.name = name; // Public property
    }
}

// 2. Create Subclass Student extending Person
class Student extends Person {
    #studentId; // Private field declaration
    #grade;     // Private field declaration

    constructor(name, studentId, grade) {
        super(name); // Call the parent constructor (Person)
        this.#studentId = studentId;
        this.#grade = grade;
    }

    // 3. Encapsulation: Getter for studentId
    get studentId() {
        return this.#studentId;
    }

    // Encapsulation: Getter for grade
    get grade() {
        return this.#grade;
    }

    // Encapsulation: Setter for grade with validation logic
    set grade(newGrade) {
        const allowedGrades = ["A", "B", "C", "F"];
        if (allowedGrades.includes(newGrade)) {
            this.#grade = newGrade;
        } else {
            console.log(`[Error] Invalid grade: ${newGrade}`);
        }
    }
}

// 4. Instantiation & Verification
console.log("--- Student Management System Execution ---");

// Instantiate Student
const student = new Student("Rahul", "VJTI-2026-CS", "B");

// Log initial details using getters
console.log("Student Name:", student.name); // Inherited public property
console.log("Student ID:", student.studentId); // Accessed via getter
console.log("Initial Grade:", student.grade); // Accessed via getter

// Attempt to set a valid grade
console.log("Updating grade to A...");
student.grade = "A"; // Triggers setter
console.log("Updated Grade:", student.grade);

// Attempt to set an invalid grade
console.log("Attempting to set invalid grade Z...");
student.grade = "Z"; // Triggers setter (validation will fail)
console.log("Grade after invalid update:", student.grade);
```

#### Explanation
* **Inheritance:** `Student` extends `Person`. The constructor calls `super(name)` to pass the name up to the `Person` class, ensuring the public variable `name` is initialized properly.
* **Encapsulation:** The fields `#studentId` and `#grade` are prefix-declared with `#`, making them private. They cannot be read or modified directly.
* **Getters & Setters:** The getter methods `get studentId()` and `get grade()` provide read access. The setter `set grade(newGrade)` restricts modification. It uses `allowedGrades.includes(newGrade)` to validate input before updating the private field `#grade`.

#### Output
```text
--- Student Management System Execution ---
Student Name: Rahul
Student ID: VJTI-2026-CS
Initial Grade: B
Updating grade to A...
Updated Grade: A
Attempting to set invalid grade Z...
[Error] Invalid grade: Z
Grade after invalid update: A
```

---

### Assignment 2: Kirana Shopping Cart Total Calculator

#### Solution Code
```javascript
// Kirana Shopping Cart Total Calculator

// 1. Declare original shopping cart items
const shoppingCart = [
    { id: 1, name: "Notebook", price: 120.00, details: { category: "Stationery" } },
    { id: 2, name: "Scientific Calculator", price: 850.00, details: { category: "Stationery" } },
    { id: 3, name: "Wireless Mouse", price: 1200.00, details: { category: "Electronics" } },
    { id: 4, name: "Water Bottle", price: 250.00 } // details is undefined
];

// 2. Single-line arrow function to check stationery using optional chaining
const isStationery = item => item.details?.category === "Stationery";

// 3. Filter items that belong to the Stationery category
const stationeryItems = shoppingCart.filter(isStationery);

// 4. Map to apply a 10% discount (multiply price by 0.9) using destructuring
const discountedPrices = stationeryItems.map(({ price }) => price * 0.9);

// 5. Reduce to calculate grand total
const grandTotal = discountedPrices.reduce((total, price) => total + price, 0);

// 6. Log output reports
console.log("--- Kirana Shopping Cart Total Calculator Execution ---");
console.log("Original Cart size:", shoppingCart.length, "items");
console.log("Filtered Stationery:", stationeryItems);
console.log("Stationery Grand Total (10% Discount Applied): ₹" + grandTotal);
```

#### Explanation
* **Optional Chaining:** The helper `isStationery` uses `item.details?.category` to prevent a crash on the last item (Water Bottle) which does not have a `details` object.
* **Filter:** `filter(isStationery)` filters out non-stationery items, returning an array of length 2 containing the Notebook and the Scientific Calculator.
* **Destructuring & Map:** Inside the map callback, `{ price }` destructures the price property from each stationery object, returning a transformed array of numeric values: `[108, 765]`.
* **Reduce:** `reduce((total, price) => total + price, 0)` iterates through the array of discounted prices and aggregates them starting from `0`, returning the final sum of `873`.

#### Output
```text
--- Kirana Shopping Cart Total Calculator Execution ---
Original Cart size: 4 items
Filtered Stationery: [
  { id: 1, name: 'Notebook', price: 120, details: { category: 'Stationery' } },
  { id: 2, name: 'Scientific Calculator', price: 850, details: { category: 'Stationery' } }
]
Stationery Grand Total (10% Discount Applied): ₹873
```

---

### Assignment 3: Employee & Manager Classes (HR Payroll)

#### Solution Code
```javascript
// Employee & Manager Classes (HR Payroll)

// 1. Abstract Base Class Simulation
class BaseStaff {
    constructor(name, baseSalary) {
        // Prevent direct instantiation of BaseStaff
        if (new.target === BaseStaff) {
            throw new Error("Cannot instantiate BaseStaff abstract class directly.");
        }
        this.name = name;
        this.baseSalary = baseSalary;
    }

    // Abstract Method Simulation
    calculateMonthlyPay() {
        throw new Error("Method 'calculateMonthlyPay()' must be implemented by subclass.");
    }
}

// 2. Employee Subclass implementing calculateMonthlyPay
class Employee extends BaseStaff {
    constructor(name, baseSalary) {
        super(name, baseSalary); // Calls parent constructor
    }

    calculateMonthlyPay() {
        return this.baseSalary; // Base staff receives standard salary
    }
}

// 3. Manager Subclass extending BaseStaff and overriding calculateMonthlyPay
class Manager extends BaseStaff {
    constructor(name, baseSalary, bonus) {
        super(name, baseSalary); // Calls parent constructor
        this.bonus = bonus;      // Managers have a unique bonus attribute
    }

    calculateMonthlyPay() {
        return this.baseSalary + this.bonus; // Pay is salary + bonus
    }
}

// 4. Verification & Polymorphism Helper
console.log("--- Employee & Manager Classes Execution ---");

// Test Abstraction: Attempt to instantiate BaseStaff directly
try {
    const abstractStaff = new BaseStaff("Unknown User", 30000);
} catch (error) {
    console.log("Caught expected error:", error.message);
}

// Instantiate child objects
const employeePriya = new Employee("Priya", 40000);
const managerVikram = new Manager("Vikram", 60000, 15000);

// Helper function to print paycheck details (demonstrates polymorphism)
function printPaycheck(staff) {
    // Calls the polymorphic method calculateMonthlyPay()
    const pay = staff.calculateMonthlyPay();
    // Uses the class constructor name to dynamically log the role
    const role = staff.constructor.name; 
    console.log(`Paycheck for ${role} ${staff.name}: Monthly Pay is ₹${pay}`);
}

// Execute helper on different instances
printPaycheck(employeePriya);
printPaycheck(managerVikram);
```

#### Explanation
* **Abstraction:** The condition `new.target === BaseStaff` inside the `BaseStaff` constructor detects if someone called `new BaseStaff()`. If so, it throws a runtime error. This successfully prevents direct instantiation.
* **Inheritance:** Both `Employee` and `Manager` extend `BaseStaff` using the `extends` keyword and pass values to the parent class via `super()`.
* **Polymorphism:** The `printPaycheck` function accepts any object that extends `BaseStaff`. When it runs `staff.calculateMonthlyPay()`, JavaScript looks up the actual object instance and executes the overridden version. Thus, `Employee` returns salary directly, while `Manager` returns salary + bonus.

#### Output
```text
--- Employee & Manager Classes Execution ---
Caught expected error: Cannot instantiate BaseStaff abstract class directly.
Paycheck for Employee Priya: Monthly Pay is ₹40000
Paycheck for Manager Vikram: Monthly Pay is ₹75000
```
