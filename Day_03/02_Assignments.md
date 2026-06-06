# Day 3: Async JavaScript and TypeScript – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of Asynchronous JavaScript, TypeScript type safety, and SOLID design principles. Please attempt all questions.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the difference between a **Promise** and a **Callback** in JavaScript? What is the "Callback Hell" (Pyramid of Doom), and how does **Async/Await** solve it?

### Question 2 🔥 **Frequently Asked**
Explain the differences between **Types** and **Interfaces** in TypeScript. When would you use a `type` alias instead of an `interface`?

### Question 3 📌 **Important**
What is the purpose of **Generics** in TypeScript? How do generics help in writing reusable and type-safe API client helper libraries or test utilities?

### Question 4 📌 **Important**
What is a **Type Assertion** in TypeScript? What is the difference between type assertion (using `as`) and type casting? Does type assertion have any effect at runtime?

### Question 5 💡 **Good to Know**
Explain the **Single Responsibility Principle (SRP)** and the **Open-Closed Principle (OCP)** from the SOLID framework. How do they apply to structuring Page Objects in Playwright test suites?

---

## Practical Assignments

### Assignment 1: Fetch Student Enrollment Data (Async/Await)

* **Interview Relevance:** Tests your capability to handle asynchronous promises, simulate network delay timeouts, and implement robust error catch recovery logic — a daily task in writing API automation scripts.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Promises, Async/Await, Error Handling (`try-catch-finally`).

#### Problem Statement
Write a script that simulates querying the VJTI College ERP server to fetch transaction details for a list of student roll numbers. The network requests are asynchronous and may fail due to database server timeouts.

#### Requirements
1. **Mock Network Promise:** Write a helper function `mockFetchStudentApi(rollNo)` that returns a Promise:
   * It should resolve after `1000ms` with a student record object if the roll number is valid:
     ```javascript
     { rollNo: rollNo, name: "Rahul", feeStatus: "PAID" }
     ```
   * If the roll number is `9999` (simulating a crash trigger), the promise should reject with a network connection error message: `[CRITICAL ERROR] Database query timed out for roll number 9999.`
2. **Async Processor Function:** Write an `async` function `processEnrollmentQuery(rollNumbers)`:
   * It should accept an array of roll numbers (e.g., `[1001, 9999, 1002]`).
   * Iterate through the roll numbers sequentially using a loop.
   * For each roll number, `await` the API response inside a `try-catch` block.
   * If the promise resolves, log the student details: `[SUCCESS] Student Record Found - Roll No: <rollNo>, Name: <name>, Status: <status>`.
   * If the promise rejects, catch the error and log a critical warning: `[WARNING] Query Failed: <errorMessage>`.
   * Use a `finally` block for each loop iteration to log: `Query transaction logs closed for Roll No: <rollNo>.`.
3. **Execution:** Run the function with the input array: `[1001, 9999, 1002]`.

#### Expected Output
```text
[SUCCESS] Student Record Found - Roll No: 1001, Name: Rahul, Status: PAID
Query transaction logs closed for Roll No: 1001.
[WARNING] Query Failed: [CRITICAL ERROR] Database query timed out for roll number 9999.
Query transaction logs closed for Roll No: 9999.
[SUCCESS] Student Record Found - Roll No: 1002, Name: Rahul, Status: PAID
Query transaction logs closed for Roll No: 1002.
```

#### Hints
* Use `for...of` loop to iterate sequentially and allow the `await` keyword to pause execution correctly inside each iteration.
* Place the `try-catch-finally` block inside the loop body, not outside.

---

### Assignment 2: VJTI Student ERP Type System (TypeScript)

* **Interview Relevance:** Verifies your ability to use TypeScript interfaces, custom enums, type assertions, and utility types to model test data schemas and API responses.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** TypeScript Types, Interfaces, Enums, Type Assertions, Utility Types.

#### Problem Statement
Define a type-safe blueprint for a college ERP system. You will build interface models representing students and courses, enforce valid registrations via enums, and use utility type helpers to construct partial update requests.

#### Requirements
1. **Department Enum:** Define a string Enum named `Department` with values:
   * `ComputerScience = "CS"`
   * `InformationTechnology = "IT"`
   * `Mechanical = "ME"`
2. **Student Interface:** Define an interface `Student` containing:
   * `name` (string)
   * `rollNo` (number)
   * `dept` (Department)
   * `hostelRoom?` (optional number)
3. **Utility Types Validation:**
   * Create a type alias `StudentUpdate` that makes all properties of `Student` optional (hint: use `Partial<T>`).
   * Create a type alias `ReadOnlyStudent` that makes all properties read-only (hint: use `Readonly<T>`).
4. **Type Assertion Simulation:**
   * Assume you fetch a raw response from a network API typed as `any`:
     ```typescript
     const rawApiResponse: any = { name: "Priya Sharma", rollNo: 1002, dept: "IT" };
     ```
   * Assert `rawApiResponse` as a valid `Student` object using the `as` syntax.
5. **Implementation Code:**
   * Create a valid `Student` object named `studentRahul` matching the `Student` interface.
   * Write an update function `updateStudentProfile(original: Student, updates: StudentUpdate): Student` that merges updates using the spread operator.
   * Try to modify properties of a `ReadOnlyStudent` instance and verify the compiler blocks it (comment out the failing code line and write a note).

#### Expected Output
```text
Original Student: Rahul, Department: CS, Room: 105
Updated Student: Rahul, Department: CS, Room: 202
Asserted Student: Priya Sharma, Department: IT
```

---

### Assignment 3: SOLID Banking Transaction System

* **Interview Relevance:** Tests structural architecture and design pattern compliance (SRP, OCP), demonstrating you can design modular Page Object frameworks that are easy to extend.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Classes, SOLID Principles (Single Responsibility, Open-Closed Principle).

#### Problem Statement
Refactor a messy billing transaction class that processes college fee payments. The class currently violates both the **Single Responsibility Principle (SRP)** (it handles transaction processing, console receipt logging, and audit file writing) and the **Open-Closed Principle (OCP)** (if you add a new payment method, you have to modify the core billing script).

#### Legacy Code to Refactor
```typescript
class LegacyFeeProcessor {
    processPayment(studentName: string, amount: number, method: string) {
        // 1. Process payment logic
        if (method === "PAYTM") {
            console.log(`Processing ₹${amount} via Paytm UPI for ${studentName}...`);
        } else if (method === "NETBANKING") {
            console.log(`Processing ₹${amount} via NetBanking for ${studentName}...`);
        }

        // 2. Logging audit log logic
        console.log(`[AUDIT LOG] ${new Date().toISOString()}: Paid ₹${amount} for ${studentName}`);
    }
}
```

#### Requirements
1. **Apply SRP:**
   * Separate the logging responsibilities. Create a distinct `AuditLogger` class that handles formatting and writing audit logs:
     ```typescript
     class AuditLogger {
         logTransaction(message: string) { /* ... */ }
     }
     ```
2. **Apply OCP:**
   * Create a common interface `PaymentMethod` that declares a method `pay(amount: number, name: string): void`.
   * Create concrete payment gateway classes implementing `PaymentMethod`:
     * `PaytmGateway` (e.g. logs `"Processing ₹<amount> via Paytm UPI..."`).
     * `NetBankingGateway` (e.g. logs `"Processing ₹<amount> via SBI NetBanking..."`).
   * Create a refactored `FeeProcessor` class that accepts a `PaymentMethod` injection. This allows adding new gateways (like CardPayment) without modifying `FeeProcessor`.
3. **Execution Script:**
   * Instantiate the `FeeProcessor` class.
   * Process a payment of `₹45000` for `"Priya"` using the `PaytmGateway`.
   * Process a payment of `₹45000` for `"Vikram"` using the `NetBankingGateway`.

#### Expected Output
```text
Processing ₹45000 via Paytm UPI for Priya...
[AUDIT LOG] Transaction Success: Paid ₹45000 for Priya
Processing ₹45000 via SBI NetBanking for Vikram...
[AUDIT LOG] Transaction Success: Paid ₹45000 for Vikram
```
