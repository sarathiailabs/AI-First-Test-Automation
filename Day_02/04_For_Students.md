# Day 2: Modern JavaScript and OOP – Student Revision Notes

This revision document is designed to help you quickly review the key concepts taught during the Day 2 lecture session.

---

## let & const

### Definition
Keywords used to declare block-scoped variables in modern JavaScript, replacing the legacy `var` keyword. *(Block-scoped declaration keywords jo var ki jagah use hote hain.)*

### Important Syntax
```javascript
let upiTransactionCount = 0; // Re-assignable variable (open container)
const collegeName = "VJTI";  // Reassignment forbidden (sealed container)
```

### Example
```javascript
let upiStatus = "pending";
const collegeCode = "VJTI-MUM";

upiStatus = "success"; // Allowed
// collegeCode = "IIT-B"; // Error!
```

### Key Points
* Respect block-level boundaries (inside `{}`).
* Accessing a variable before declaring it throws a `ReferenceError` (Temporal Dead Zone).
* Non-primitive values declared with `const` (like objects and arrays) can have their fields modified, but the variable cannot be reassigned to a different structure.

### Common Mistakes
* **Reassigning const primitive values:** Generates a `TypeError`. Use `let` if the value needs to change.

---

## Arrow Functions

### Definition
A shorter syntax for writing function expressions using the `=>` operator. *(Function likhne ka chota aur aasan tarika jo lexical 'this' use karta hai.)*

### Important Syntax
```javascript
const name = (params) => expression; // Implicit return (single line)
```

### Example
```javascript
const calculateDeliveryFee = distance => distance * 12;
const greetStudent = (name) => {
    return `Namaste, ${name}`;
};
```

### Key Points
* Omitting `{}` and `return` allows automatic implicit returns.
* Arrow functions do not bind their own `this`. They inherit `this` from the surrounding context (lexical `this`).

### Common Mistakes
* **Using arrow functions as object methods:** Since they lack their own `this`, writing object methods as arrow functions can cause `this` to point to the global window object instead of the local class.

---

## Template Literals

### Definition
String templates that use backticks (`` ` ``) instead of quotes, allowing expression interpolation and multi-line strings. *(Backticks use karke dynamic string aur variable print karne ka smart tarika.)*

### Important Syntax
```javascript
const str = `Value is: ${expression}`;
```

### Example
```javascript
const studentName = "Rahul Verma";
const reportMessage = `College: VJTI Mumbai
Student Name: ${studentName}
Marks Checked: ${new Date().toLocaleDateString()}`;
```

### Key Points
* Use `${}` to embed variables or evaluations.
* Preserves original whitespace, indentations, and newlines.

### Common Mistakes
* **Using single/double quotes instead of backticks:** String interpolation `${}` will print literally as text.

---

## Destructuring

### Definition
An ES6 syntax that unpacks values from arrays or properties from objects into separate variables. *(Object ya Array me se variables ko direct nikalne ka simple syntax.)*

### Important Syntax
```javascript
const { key1, key2: customName } = object;
const [firstItem, , thirdItem] = array;
```

### Example
```javascript
const studentProfile = { name: "Rahul Verma", rollNo: 42, branch: "CS" };
const { name, branch: dept, hostelRoom = 101 } = studentProfile;
```

### Key Points
* Key matching is critical for objects. Rename using `key:newName`.
* Array destructuring is position-based. Skips elements using commas.

### Common Mistakes
* **Destructuring from null/undefined:** Triggers a `TypeError` crash. Ensure objects are initialized.

---

## Spread Operator

### Definition
An operator (`...`) used to expand an array or object into separate individual values or attributes. *(Array ya Object ko expand karne ya merge karne ka tool.)*

### Important Syntax
```javascript
const copy = { ...original };
const joined = [...arr1, ...arr2];
```

### Example
```javascript
const baseConfig = { timeout: 3000, headless: true };
const devConfig = { ...baseConfig, headless: false, retries: 2 };
```

### Key Points
* Performs a **shallow copy** (nested objects or arrays are copied by reference, not values).
* Useful for copying or merging settings without altering base setups.

### Common Mistakes
* **Assuming deep cloning:** Changing nested properties in the cloned object will affect the original.

---

## Rest Operator

### Definition
An operator (`...`) used to gather remaining arguments or destructured values into a single array structure. *(Bache hue arguments ko ek array me ek sath collect karne ka tarika.)*

### Important Syntax
```javascript
function run(first, ...others) {}
```

### Example
```javascript
function printAbsentees(className, ...rollNumbers) {
    console.log(`Class ${className} absentees:`, rollNumbers);
}
printAbsentees("CS-Batch A", 45, 12, 89);
```

### Key Points
* Allows creating functions that take a dynamic number of arguments.
* Must be placed as the last parameter in functions or destructuring targets.

### Common Mistakes
* **Placing the rest parameter in the middle:** `function test(...args, callback) {}` is a `SyntaxError`.

---

## Optional Chaining

### Definition
An operator (`?.`) that short-circuits evaluation and returns `undefined` if a nested property node in a reference chain is null/undefined. *(Nested properties ko call karte waqt crash se bachane wala checking tool.)*

### Important Syntax
```javascript
const city = student?.profile?.address?.city;
```

### Example
```javascript
const response = { status: 200, student: null };
console.log(response.student?.profile?.name); // Prints undefined instead of crashing
```

### Key Points
* Prevents crashes (`Cannot read properties of undefined`).
* Can be used on optional functions: `callback?.()`.

### Common Mistakes
* **Using optional chaining for assignments:** `obj?.property = value` is a syntax error.

---

## map()

### Definition
An array method that transforms each element of an array 1-to-1, returning a new array of matching length. *(Array ke har element ko badal kar ek naya equal-sized array banane ka method.)*

### Important Syntax
```javascript
const output = array.map(item => transformedValue);
```

### Example
```javascript
const ticketPrices = [120, 850];
const discountedPrices = ticketPrices.map(price => price * 0.9); // [108, 765]
```

### Key Points
* Highly functional and leaves the original array unmodified.
* Must return a value inside the callback.

### Common Mistakes
* **Using map for side effects:** If you just want to run loops with no return array, use `forEach()`.

---

## filter()

### Definition
An array method that filters elements based on a logical check, returning a new array with elements that evaluate to true. *(Condition lagakar array me se sirf matching items ko filter karne ka method.)*

### Important Syntax
```javascript
const matches = array.filter(item => condition);
```

### Example
```javascript
const studentMarks = [
    { name: "Rahul", marks: 85 },
    { name: "Amit", marks: 32 }
];
const passedStudents = studentMarks.filter(student => student.marks >= 35);
```

### Key Points
* Original array is unmodified.
* The callback must return a truthy/falsy value.

### Common Mistakes
* **Using filter to find a single element:** `filter` always returns an array. Use `find()` to retrieve the item itself.

---

## reduce()

### Definition
An array method that executes a reducer function on each element, accumulating values to output a single result. *(Array ke saare elements ko mila kar ek single value banane ka method.)*

### Important Syntax
```javascript
const total = array.reduce((acc, curr) => acc + curr, initialValue);
```

### Example
```javascript
const stationaryPrices = [120, 850, 100];
const grandTotal = stationaryPrices.reduce((sum, price) => sum + price, 0); // 1070
```

### Key Points
* Accumulator stores the running result.
* Always define the initial value (especially when processing objects) to avoid NaN errors.

### Common Mistakes
* **Omitting the initial value parameter:** This uses the first array element as the initial accumulator, which causes type conversion errors when handling arrays of objects.

---

## Scope

### Definition
The environment context determining where variables can be accessed. *(Code me variables kahan dikhenge aur kahan access ho sakte hain uski boundary.)*

### Example
```javascript
const collegeName = "VJTI";

function checkScope() {
    let wardenName = "Dr. Prasad";
    if (true) {
        let roomNo = "Room 105";
    }
    // console.log(roomNo); // Error: roomNo is not defined
}
```

### Key Points
* Global scope: Visible everywhere.
* Function scope: Visible only inside function bodies.
* Block scope: Visible only inside `{}` brackets (applies to `let` and `const`).

### Common Mistakes
* **Accessing block-scoped variables outside their block:** Trying to use `let`/`const` variables outside their loops or conditions throws reference errors.

---

## Closures

### Definition
A function's ability to retain and access variables from its outer lexical parent scope even after that parent has returned. *(Inner function ka apne outer function ke variables ko yaad rakhna, outer function ke khatam hone ke baad bhi.)*

### Example
```javascript
function createCounter(name) {
    let count = 0;
    return function() {
        count++;
        return `${name}-${count}`;
    };
}
const checkInCounter = createCounter("Rahul");
console.log(checkInCounter()); // Rahul-1
```

### Key Points
* Encapsulates state securely.
* Allows creating factories or dynamic setup generators.

### Common Mistakes
* **Memory leak overheads:** Closures retain scope variables, preventing garbage collection. Avoid keeping large arrays or objects in scopes that run indefinitely.

---

## Classes

### Definition
A template blueprint for creating objects containing initialized properties and methods. *(Naye objects banane ke liye ek structured blueprint ya design map.)*

### Important Syntax
```javascript
class ClassName {
    constructor(param) {
        this.property = param;
    }
}
```

### Example
```javascript
class HostelRoom {
    constructor(roomNo) {
        this.roomNo = roomNo;
    }
    checkIn() {
        console.log(`Checking in to ${this.roomNo}`);
    }
}
const room105 = new HostelRoom(105);
```

### Key Points
* `constructor` executes automatically upon `new` instantiation.
* Use `static` to declare methods called directly on the class name.

### Common Mistakes
* **Forgetting the `new` keyword:** Calling `Class()` without `new` throws a `TypeError`.

---

## Objects

### Definition
An active instance of a class containing concrete state values and methods. *(Class se bani hui real entities jo variables (state) aur functions (behavior) ko combine karti hain.)*

### Example
```javascript
const room105 = new HostelRoom(105);
room105.checkIn();
```

### Key Points
* Represents distinct entities sharing behaviors but holding independent states.

### Common Mistakes
* **Assuming objects share states:** State modification on one instance does not affect separate instances.

---

## Encapsulation

### Definition
Restricting direct access to internal class details, using private variables and methods while exposing safe public APIs. *(Data ko class ke andar chupa kar rakhna aur public methods ke bina access na hone dena.)*

### Important Syntax
```javascript
class ClassName {
    #privateField; // Must be declared here
}
```

### Example
```javascript
class SbiAccount {
    #balance = 0;
    deposit(amount) {
        if (amount > 0) this.#balance += amount;
    }
    get balance() {
        return this.#balance;
    }
}
```

### Key Points
* Private fields use the `#` prefix.
* Access private variables externally using getter (`get`) and setter (`set`) methods.

### Common Mistakes
* **Accessing private variables directly:** Calling `account.#balance` externally triggers a compilation syntax crash.

---

## Inheritance

### Definition
A class acquisition mechanism allowing a child class to extend parent properties and methods. *(Ek class (child) ka dusri class (parent) ke features aur functions ko reuse karna.)*

### Important Syntax
```javascript
class SubClass extends ParentClass {
    constructor() {
        super(); // Runs parent constructor
    }
}
```

### Example
```javascript
class GeneralBusPass {
    print() { console.log("Printing General Pass"); }
}
class StudentBusPass extends GeneralBusPass {}
const pass = new StudentBusPass();
pass.print(); // Printing General Pass
```

### Key Points
* The `extends` keyword hooks parent relationships.
* Calling `super()` is mandatory inside child constructors before accessing `this`.

### Common Mistakes
* **Forgetting `super()` inside the constructor:** Throws a `ReferenceError` when trying to reference `this`.

---

## Polymorphism

### Definition
The ability of different classes to respond to the exact same method signature in their own specific way. *(Same method name par different classes ka apna-apna alag tareeke se kaam karna.)*

### Example
```javascript
class PaytmUPI {
    pay(amount) { console.log(`Paytm UPI Payment of ₹${amount}`); }
}
class CardPayment {
    pay(amount) { console.log(`Card Payment of ₹${amount}`); }
}
```

### Key Points
* Relies on child classes overriding parent method names.
* Allows client code to invoke methods consistently without needing to know specific subclass types.

### Common Mistakes
* **Method signature mismatch:** Changing the method name or parameters in subclass implementations breaks polymorphic calls.

---

## Abstraction

### Definition
Exposing high-level features while hiding low-level details. *(Internal coding details ko chupa kar user ko sirf aasan aur important options dikhana.)*

### Example
```javascript
class AtmMachine {
    constructor() {
        if (new.target === AtmMachine) {
            throw new Error("Cannot instantiate abstract ATM.");
        }
    }
}
```

### Key Points
* Reduces cognitive complexity for client scripts.
* Simulated in JS by checking `new.target` and throwing errors on un-implemented methods.

### Common Mistakes
* **Exposing internal locator variables:** Keep page object locators encapsulated; expose only simple semantic methods like `login()` or `submit()`.

---

## Assignment Summary

* **Student Management System (College ERP)**
  * *Concepts Practiced:* Classes, Private Fields (`#studentId`, `#grade`), Encapsulation (Getters/Setters), and Base Class Inheritance (`extends`, `super()`).
* **Kirana Shopping Cart Total Calculator**
  * *Concepts Practiced:* Optional Chaining (`?.`), callback logic with arrow functions, Object Destructuring (`{ price }`), array transformations (`filter`, `map`), and accumulator aggregations (`reduce`).
* **Employee & Manager Classes (HR Payroll)**
  * *Concepts Practiced:* Abstract class simulations (`new.target`), Inheritance, and Polymorphic method execution (`calculateMonthlyPay()`).

---

## Quick Revision Sheet

* **Variables & Interpolation:**
  ```javascript
  const rate = 0.9;
  const message = `Discount rate is: ${rate * 100}%`;
  ```
* **Array Processing:**
  ```javascript
  const stationery = cart.filter(i => i.details?.cat === "Stationery");
  const prices = stationery.map(({ price }) => price);
  const total = prices.reduce((acc, p) => acc + p, 0);
  ```
* **Class Structures & Privacy:**
  ```javascript
  class Component {
      #selector;
      constructor(sel) { this.#selector = sel; }
      get selector() { return this.#selector; }
  }
  class Button extends Component {}
  ```

---

## Important Takeaways

1. Modern JavaScript structures like optional chaining (`?.`) and destructuring make test scripts more robust and readable.
2. Declarative array methods (`map`, `filter`, `reduce`) avoid verbose loops and promote code cleanliness.
3. OOP principles (Encapsulation, Inheritance, Polymorphism, Abstraction) allow us to build modular and reusable Page Objects in test automation frameworks.
4. Using private fields (`#`) prevents test scripts from directly changing internal page states, ensuring data remains clean.
