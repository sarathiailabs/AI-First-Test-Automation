# Day 2: Modern JavaScript and OOP – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of Modern JavaScript and Object-Oriented Programming (OOP) taught in the Session. Please attempt all questions and implement the coding assignments.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is a **Closure** in JavaScript? Explain the lexical environment concept. How can closures help you build private configuration managers or test status trackers in an automation framework?

### Question 2 🔥 **Frequently Asked**
What is the difference between the **Spread operator (`...`)** and the **Rest operator (`...`)** in JavaScript? Provide short syntax snippets for both. Can they be used together in the same line of code?

### Question 3 📌 **Important**
How do `map()`, `filter()`, and `reduce()` differ in terms of their return values and use cases? Write down the return type of each method. Why are they preferred over standard `for` loops in modern automation test assertions?

### Question 4 📌 **Important**
What is the purpose of **Encapsulation** in Object-Oriented Programming? How do you implement private fields in modern JavaScript classes (ES6+), and what error is thrown if you attempt to access them directly from outside the class?

### Question 5 💡 **Good to Know**
How does **Polymorphism** differ from **Inheritance**? Explain how method overriding and polymorphic execution are used to implement common browser actions or multi-environment configuration loading.

---

## Practical Assignments

### Assignment 1: Student Management System (College ERP)

* **Interview Relevance:** Tests encapsulation, class instantiation, inheritance, and validation logic using getters/setters — a core building block for designing Page Objects and test state managers.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Classes, Objects, Private Fields (`#`), Encapsulation (Getters/Setters), Inheritance.

#### Problem Statement
Create a script to model students inside a college ERP database system. This assignment will test your ability to structure objects using OOP, encapsulate private records (like roll numbers and grades), and inherit common attributes from a base person class.

#### Requirements
1. **Base Class:** Create a class named `Person` with a constructor that initializes a public property `name`.
2. **Subclass:** Create a class named `Student` that extends `Person`:
   * Add a private field `#studentId` to store their enrollment ID.
   * Add a private field `#grade` to store their letter grade (e.g., `"A"`, `"B"`, `"C"`).
   * The constructor of `Student` should accept `name`, `studentId`, and `grade`. It must call the parent constructor using `super()`.
3. **Encapsulation:**
   * Create a getter for `studentId` to read the private ID value.
   * Create a getter for `grade` to read the private grade.
   * Create a setter for `grade` that validates the new grade value before assignment. The setter should only allow grades `"A"`, `"B"`, `"C"`, or `"F"`. If the incoming value is invalid, print an error message: `[Error] Invalid grade: <value>`.
4. **Instantiation & Verification:**
   * Instantiate a new `Student` named `"Rahul"` with ID `"VJTI-2026-CS"` and grade `"B"`.
   * Log the student's name, ID, and initial grade using the getters.
   * Attempt to update the student's grade to `"A"` (valid) and log the updated grade.
   * Attempt to update the student's grade to `"Z"` (invalid) and verify that the grade does not change and prints the error message.

#### Expected Output
```text
Student Name: Rahul
Student ID: VJTI-2026-CS
Initial Grade: B
Updating grade to A...
Updated Grade: A
Attempting to set invalid grade Z...
[Error] Invalid grade: Z
Grade after invalid update: A
```

#### Hints
* Remember to call `super(name)` at the very beginning of the `Student` constructor.
* Declare the private fields with `#` at the top of the `Student` class body before the constructor (e.g., `#studentId;` and `#grade;`).

---

### Assignment 2: Kirana Shopping Cart Total Calculator

* **Interview Relevance:** Tests higher-order array methods (`map`, `filter`, `reduce`), optional chaining, and destructuring — which are tested in almost all modern JavaScript machine coding rounds for Automation roles.
* **Difficulty Level:** Beginner-Intermediate
* **Concepts Covered:** let & const, Arrow Functions, Destructuring, Optional Chaining, `map()`, `filter()`, `reduce()`.

#### Problem Statement
Write a script that processes an array of items representing a shopping cart from a grocery (Kirana) delivery app automation test. You need to filter out items that aren't in the "Stationery" category, apply a 10% discount to the remaining Stationery items, and calculate the final grand total.

#### Requirements
1. Declare a constant array named `shoppingCart` containing at least four item objects:
   ```javascript
   const shoppingCart = [
       { id: 1, name: "Notebook", price: 120.00, details: { category: "Stationery" } },
       { id: 2, name: "Scientific Calculator", price: 850.00, details: { category: "Stationery" } },
       { id: 3, name: "Wireless Mouse", price: 1200.00, details: { category: "Electronics" } },
       { id: 4, name: "Water Bottle", price: 250.00 } // Note: details is undefined
   ];
   ```
2. Write a single-line arrow function named `isStationery` that checks if an item belongs to the `"Stationery"` category. Use **optional chaining** (`?.`) to safely inspect the nested category property.
3. Filter the shopping cart items to keep only items belonging to the `"Stationery"` category.
4. Using the `map()` method, transform the filtered stationery array into a new array of discounted prices. The discount factor should be 10% off (multiply each price by `0.9`). Use **object destructuring** inside the map callback to extract the `price` property.
5. Using the `reduce()` method, sum the discounted prices to get the total cost. Set the initial accumulator value of the reduce function to `0`.
6. Log the original cart size, the filtered stationery array, and the final grand total to the console in a clear format.

#### Expected Output
```text
Original Cart size: 4 items
Filtered Stationery: [
  { id: 1, name: 'Notebook', price: 120, details: { category: 'Stationery' } },
  { id: 2, name: 'Scientific Calculator', price: 850, details: { category: 'Stationery' } }
]
Stationery Grand Total (10% Discount Applied): ₹873
```

#### Hints
* Category verification should check `item.details?.category === "Stationery"`.
* Destructure the price property in your mapping function: `({ price }) => price * 0.9`.

---

### Assignment 3: Employee & Manager Classes (HR Payroll)

* **Interview Relevance:** Verifies your capability to design classes using polymorphism and abstraction constraints — simulating clean modular architecture that separates basic entities from specialized entities.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Classes, Objects, Inheritance, Polymorphism, Abstraction.

#### Problem Statement
Design a payroll simulation for an engineering company. Write an abstract `BaseStaff` class to represent employees, an `Employee` subclass for standard staff, and a `Manager` subclass that overrides payroll logic to add bonuses. Use basic abstraction constraints to prevent creating raw `BaseStaff` objects.

#### Requirements
1. **Abstraction Simulation:**
   * Create an abstract class named `BaseStaff`:
     * Throw an error if a developer tries to instantiate `BaseStaff` directly: `Cannot instantiate BaseStaff abstract class directly.`
     * Declare an abstract method `calculateMonthlyPay()` that throws an error: `Method 'calculateMonthlyPay()' must be implemented by subclass.`
     * The class constructor should initialize public properties `name` and `baseSalary` (number).
2. **Employee Subclass:**
   * Create a class `Employee` that extends `BaseStaff`.
   * Implement `calculateMonthlyPay()` to return the `baseSalary` directly.
3. **Manager Subclass:**
   * Create a class `Manager` that extends `BaseStaff`.
   * The constructor should accept `name`, `baseSalary`, and a numeric `bonus`.
   * Call `super()` to initialize the name and baseSalary.
   * Store the `bonus` as an instance property.
   * Override `calculateMonthlyPay()` to return the sum of `baseSalary` and `bonus`.
4. **Instantiation & Verification:**
   * Try to instantiate `BaseStaff` directly and verify it throws the error inside a `try-catch` block.
   * Instantiate an `Employee` named `"Priya"` with a base salary of `40000`.
   * Instantiate a `Manager` named `"Vikram"` with a base salary of `60000` and a bonus of `15000`.
   * Create a helper function `printPaycheck(staff)` that calls the polymorphic method `calculateMonthlyPay()` and logs the paycheck details.
   * Run the helper function on both Priya and Vikram.

#### Expected Output
```text
Caught expected error: Cannot instantiate BaseStaff abstract class directly.
Paycheck for Employee Priya: Monthly Pay is ₹40000
Paycheck for Manager Vikram: Monthly Pay is ₹75000
```

#### Hints
* Use `new.target` inside the `BaseStaff` constructor to check if it's being invoked directly.
* Use `super(name, baseSalary)` inside the child constructors.
