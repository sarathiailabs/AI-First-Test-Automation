# Day 3: Async JavaScript and TypeScript – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and programming assignments provided for Day 3.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is the difference between a **Promise** and a **Callback** in JavaScript? What is the "Callback Hell" (Pyramid of Doom), and how does **Async/Await** solve it?

### Answer
* **Callback:** A callback is a function passed as an argument to another function, which is executed after an asynchronous task completes.
* **Promise:** A Promise is an ES6 object representing the eventual completion or failure of an async operation. It provides structured `.then()` and `.catch()` chains, avoiding passing callbacks inside parameters.
* **Callback Hell:** When multiple asynchronous operations depend on previous results, nested callbacks create deeply indented code structures (resembling a pyramid `>`). This code is highly unreadable, difficult to debug, and fragile.
* **Async/Await Solution:** Async/Await allows developers to write asynchronous code sequentially without indentations. The `await` keyword pauses execution, waiting for the promise resolution, converting nested callbacks into simple, linear lines of code.

---

### Question 2 🔥 **Frequently Asked**
Explain the differences between **Types** and **Interfaces** in TypeScript. When would you use a `type` alias instead of an `interface`?

### Answer
* **Declaration & Extension:**
  * **Interfaces** are defined using the `interface` keyword and can be extended via inheritance (`extends`) or merged by declaring the same interface name multiple times (Declaration Merging). They represent object shape contracts.
  * **Types** are defined using the `type` alias keyword and combine structures using intersection (`&`) or union (`|`) operators. They cannot be merged via declaration merging.
* **When to use Type Aliases:**
  * To define union types (e.g. `type Status = "success" | "failed"`).
  * To define tuple types or map primitive values (e.g. `type RollNumber = number`).
* **When to use Interfaces:**
  * To model class contracts or complex objects that will be extended in other libraries or Page Object models (POM).

---

### Question 3 📌 **Important**
What is the purpose of **Generics** in TypeScript? How do generics help in writing reusable and type-safe API client helper libraries or test utilities?

### Answer
The purpose of **Generics** is to write reusable templates (classes, interfaces, functions) that operate on dynamic, placeholder types (`<T>`) rather than committing to a single type (like `any` or `string`).

#### Reusable API Client Helper:
When writing an API client for test automation, different endpoints return different data structures (e.g., one returns student profiles, another returns transaction receipt logs). Instead of writing separate fetch functions for each model, you can write a generic request function:
```typescript
async function fetchResponse<T>(url: string): Promise<T> {
    const res = await fetch(url);
    const data = await res.json();
    return data as T; // Dynamically casts result to target type
}
```
This single client function is completely type-safe and reusable across all test data models.

---

### Question 4 📌 **Important**
What is a **Type Assertion** in TypeScript? What is the difference between type assertion (using `as`) and type casting? Does type assertion have any effect at runtime?

### Answer
* **Type Assertion (`as`):** Is a compile-time signal telling the TypeScript compiler: *"Trust me, I know the type of this variable better than you do."*
* **Type Casting:** Refers to active runtime value conversion (e.g., using `Number("120")` to cast a string to a number).
* **Runtime Effect:** Type Assertion has **zero effect at runtime**. During compilation, TypeScript strips away all `as` assertions. It does not perform checks or throw exceptions if the asserted type does not match the actual runtime shape.

---

### Question 5 💡 **Good to Know**
Explain the **Single Responsibility Principle (SRP)** and the **Open-Closed Principle (OCP)** from the SOLID framework. How do they apply to structuring Page Objects in Playwright test suites?

### Answer
* **Single Responsibility Principle (SRP):** A class should do one job and have only one reason to change. In Playwright, your Page Object classes (like `LoginPage`) should only handle browser actions (entering text, clicking buttons). They should not assert test results or connect to testing databases. Assertions belong in the test script files.
* **Open-Closed Principle (OCP):** Software entities should be open for extension but closed for modification. In test setups, if you need to load environment configs, you can define a base configuration loader. If you add a new staging environment, you should extend the base class without modifying the existing, working production config loader code, preventing regression bugs.

---

## Programming Assignment Solutions

### Assignment 1: Fetch Student Enrollment Data (Async/Await)

#### Solution Code
```javascript
// 1. Mock Network Promise representing VJTI ERP server
const mockFetchStudentApi = (rollNo) => {
    return new Promise((resolve, reject) => {
        console.log(`[NETWORK] Querying VJTI Database for Roll No: ${rollNo}...`);
        setTimeout(() => {
            if (rollNo === 9999) {
                // Simulate crash trigger
                reject(`[CRITICAL ERROR] Database query timed out for roll number 9999.`);
            } else {
                resolve({ rollNo: rollNo, name: "Rahul", feeStatus: "PAID" });
            }
        }, 1000);
    });
};

// 2. Async Processor Function
async function processEnrollmentQuery(rollNumbers) {
    console.log("--- Starting Enrollment Query Cycle ---");
    
    for (const rollNo of rollNumbers) {
        try {
            const student = await mockFetchStudentApi(rollNo); // Await resolution
            console.log(`[SUCCESS] Student Record Found - Roll No: ${student.rollNo}, Name: ${student.name}, Status: ${student.feeStatus}`);
        } catch (error) {
            console.log(`[WARNING] Query Failed: ${error}`);
        } finally {
            console.log(`Query transaction logs closed for Roll No: ${rollNo}.`);
        }
    }
    
    console.log("--- Enrollment Query Cycle Complete ---");
}

// 3. Execution Script
const targetRolls = [1001, 9999, 1002];
processEnrollmentQuery(targetRolls);
```

#### Explanation
* **Promises & Simulation:** `mockFetchStudentApi` uses `setTimeout` inside a Promise construction. If `rollNo` is 9999, it rejects via `reject()`; otherwise, it resolves.
* **Async Loop Processing:** We use `for...of` instead of `forEach()`. A `forEach` loop is not promise-aware and executes all queries in parallel without pausing. `for...of` awaits the API resolution step-by-step.
* **Try-Catch-Finally:** The `try-catch-finally` block isolates the crash trigger of roll number 9999, preventing the program from crashing and allowing subsequent roll numbers (like 1002) to process successfully.

#### Output
```text
--- Starting Enrollment Query Cycle ---
[NETWORK] Querying VJTI Database for Roll No: 1001...
[SUCCESS] Student Record Found - Roll No: 1001, Name: Rahul, Status: PAID
Query transaction logs closed for Roll No: 1001.
[NETWORK] Querying VJTI Database for Roll No: 9999...
[WARNING] Query Failed: [CRITICAL ERROR] Database query timed out for roll number 9999.
Query transaction logs closed for Roll No: 9999.
[NETWORK] Querying VJTI Database for Roll No: 1002...
[SUCCESS] Student Record Found - Roll No: 1002, Name: Rahul, Status: PAID
Query transaction logs closed for Roll No: 1002.
--- Enrollment Query Cycle Complete ---
```

---

### Assignment 2: VJTI Student ERP Type System (TypeScript)

#### Solution Code
```typescript
// 1. Department Enum
enum Department {
    ComputerScience = "CS",
    InformationTechnology = "IT",
    Mechanical = "ME"
}

// 2. Student Interface
interface Student {
    name: string;
    rollNo: number;
    dept: Department;
    hostelRoom?: number; // Optional property
}

// 3. Utility Types Validation
type StudentUpdate = Partial<Student>; // All fields optional
type ReadOnlyStudent = Readonly<Student>; // All fields read-only

// 4. Update Profile Function (SRP Compliant)
function updateStudentProfile(original: Student, updates: StudentUpdate): Student {
    return { ...original, ...updates }; // Merges updates
}

// 5. Instantiation & Testing
console.log("--- Student ERP Type System Execution ---");

// Valid Student Creation
const studentRahul: Student = {
    name: "Rahul",
    rollNo: 1005,
    dept: Department.ComputerScience,
    hostelRoom: 105
};

console.log(`Original Student: ${studentRahul.name}, Department: ${studentRahul.dept}, Room: ${studentRahul.hostelRoom}`);

// Perform profile updates
const profileUpdates: StudentUpdate = { hostelRoom: 202 };
const updatedRahul = updateStudentProfile(studentRahul, profileUpdates);
console.log(`Updated Student: ${updatedRahul.name}, Department: ${updatedRahul.dept}, Room: ${updatedRahul.hostelRoom}`);

// 6. Type Assertion Simulation
const rawApiResponse: any = { name: "Priya Sharma", rollNo: 1002, dept: Department.InformationTechnology };
const assertedStudent = rawApiResponse as Student; // Asserting any to Student contract
console.log(`Asserted Student: ${assertedStudent.name}, Department: ${assertedStudent.dept}`);

// 7. ReadOnly Verification
const studentVikram: ReadOnlyStudent = {
    name: "Vikram",
    rollNo: 1003,
    dept: Department.Mechanical
};

// The following line will fail compilation if uncommented:
// studentVikram.name = "Vikram Malhotra"; 
// COMPILER ERROR: Cannot assign to 'name' because it is a read-only property.
```

#### Explanation
* **Enums & Interfaces:** The `Department` enum enforces a specific department set. The `Student` interface requires `name`, `rollNo`, and `dept` keys, while making `hostelRoom` optional.
* **Partial Mapping:** `Partial<Student>` creates a type where all fields are optional, allowing `profileUpdates` to only specify `{ hostelRoom: 202 }` without compiler errors.
* **Type Assertion (`as`):** Casts the dynamic `any` payload from the mock server response into the `Student` interface schema, restoring IDE autocomplete and warnings.

#### Output
```text
--- Student ERP Type System Execution ---
Original Student: Rahul, Department: CS, Room: 105
Updated Student: Rahul, Department: CS, Room: 202
Asserted Student: Priya Sharma, Department: IT
```

---

### Assignment 3: SOLID Banking Transaction System

#### Solution Code
```typescript
// 1. OCP Payment Method Interface
interface PaymentMethod {
    pay(amount: number, name: string): void;
}

// 2. Concrete Gateway: Paytm UPI (OCP Extension)
class PaytmGateway implements PaymentMethod {
    pay(amount: number, name: string): void {
        console.log(`Processing ₹${amount} via Paytm UPI for ${name}...`);
    }
}

// Concrete Gateway: NetBanking (OCP Extension)
class NetBankingGateway implements PaymentMethod {
    pay(amount: number, name: string): void {
        console.log(`Processing ₹${amount} via SBI NetBanking for ${name}...`);
    }
}

// 3. SRP Compliant Audit Logger Service
class AuditLogger {
    logTransaction(studentName: string, amount: number) {
        console.log(`[AUDIT LOG] Transaction Success: Paid ₹${amount} for ${studentName}`);
    }
}

// 4. Refactored FeeProcessor (DIP & OCP Compliant)
class FeeProcessor {
    private logger = new AuditLogger(); // Dependency Injection could be used here as well

    // Accepts any class that implements the PaymentMethod interface
    processPayment(studentName: string, amount: number, paymentGateway: PaymentMethod) {
        paymentGateway.pay(amount, studentName); // Delegate payment processing
        this.logger.logTransaction(studentName, amount); // Delegate logging (SRP)
    }
}

// 5. Instantiation & Verification
console.log("--- SOLID Billing System Execution ---");

const feeProcessor = new FeeProcessor();
const paytm = new PaytmGateway();
const netBanking = new NetBankingGateway();

// Process Priya's payment via Paytm
feeProcessor.processPayment("Priya", 45000, paytm);

// Process Vikram's payment via NetBanking
feeProcessor.processPayment("Vikram", 45000, netBanking);
```

#### Explanation
* **SRP Applied:** `FeeProcessor` no longer logs transaction audits. Logging is isolated inside the `AuditLogger` class, so database/file logging configurations can change without modifying the payment logic.
* **OCP Applied:** `FeeProcessor` does not check hardcoded gateway conditions (like `if (method === "PAYTM")`). Instead, it receives an object implementing `PaymentMethod` interface. If the college wants to add Card Payments later, we can create a `CardPaymentGateway` class extending `PaymentMethod` without altering the `FeeProcessor` code.

#### Output
```text
--- SOLID Billing System Execution ---
Processing ₹45000 via Paytm UPI for Priya...
[AUDIT LOG] Transaction Success: Paid ₹45000 for Priya
Processing ₹45000 via SBI NetBanking for Vikram...
[AUDIT LOG] Transaction Success: Paid ₹45000 for Vikram
```
