# Day 11: API Testing Fundamentals – Trainer Guide

This guide is designed to help you deliver a highly interactive, 2-hour session on API Testing Fundamentals. Follow the whiteboard plans, engagement scripts, and live coding exercises to keep students active and learning.

---

## Session Opening

### Welcome Script
> *"Good evening, everyone! Welcome to Day 11. Over the last few days, we automated UI screens—clicking buttons, filling forms, and scrolling. Today, we are going to look behind the curtain. We are going to learn how systems talk to other systems without any UI. Today is all about API Testing Fundamentals!"*

### Session Goal
Today's goal is to move from UI automation to backend API automation. Students will learn the mechanics of HTTP requests (headers, query parameters, request bodies), REST architecture, HTTP methods (GET, POST, PUT, PATCH, DELETE), and how to write automated checks in Playwright to validate them.

### Motivation
> *"Imagine you build a beautiful UI for a UPI payment app like GPay. It has a shining green 'Pay' button. But when you click it, the backend database fails to deduct or transfer the money. A beautiful button is useless if the underlying API fails. APIs run the show, and testing them is the most in-demand skill in the QA industry today. Let's master it!"*

---

## Topic 1: API Fundamentals

### Trainer Introduction
> *"Think of an API as a messenger. If you go to a restaurant, you don't go into the kitchen to cook or grab your plate. You sit at a table, look at a menu, and tell the waiter what you want. The waiter takes your order to the kitchen, and brings the food back. The waiter is the API. In software, when a frontend app needs student data from a database, it sends an API request, and the server returns a response."*

### Student Engagement Questions
1. *"When you check your bank balance on an SBI ATM screen, how does the ATM screen get your account details?"*
2. *"Why can't the ATM screen directly connect to the main bank database in Mumbai?"*
3. *"What would happen if the bank UI is updated? Do they need to rewrite the banking database logic too?"*

### Whiteboard Teaching
Draw a simple client-server layout:
```text
  [ CLIENT ]                  [ API WAITER ]                  [ SERVER ]
 (GPay UI App)  ──► Request (Send Rs. 500) ──►  (Processes database)
 (GPay UI App)  ◄── Response (Success Transaction) ◄── (Returns status code)
```

### Teaching Flow
1. Introduce the client-server concept.
2. Explain the Request-Response cycle.
3. Contrast APIs (raw data) vs UI (visual layout).
4. Run a simple browser fetch demo.

### Live Coding Demonstration
#### Step 1: Type
Open the browser console and run:
```javascript
const response = await fetch('https://jsonplaceholder.typicode.com/users/1');
const data = await response.json();
console.log(data.name);
```
#### Step 2: Explain
Explain that `fetch` is a built-in browser API client. We hit an endpoint, convert the response stream to JSON, and extract the student's name.
#### Step 3: Expected Output
```text
Leanne Graham
```
#### Step 4: Questions
*"Did we open any new web pages to get this name?"*
#### Step 5: Mini Exercise
Ask students to modify the URL path to `/users/2` and print the username instead of the name.

### Common Student Doubts
* **Student:** *"Is API a programming language?"*
* **Trainer:** *"No, an API is a communication protocol/interface. It is usually written in Java, Python, or JavaScript, but it transmits data in a generic text format like JSON."*

### Real World Discussion
Discuss how Swiggy uses Google Maps APIs to track delivery executives rather than building its own mapping satellite systems.

### Interview Talking Points
* Define Client, Server, Request, and Response.
* Why is API testing faster than UI testing? (No page rendering or DOM paint time).

### Topic Recap
An API is a communication bridge using a request-response pattern.

### Transition Script
> *"Now that we know what an API is, let's look at the industry standard design rules for web APIs: REST Architecture."*

---

## Topic 2: REST Architecture

### Trainer Introduction
> *"Imagine if every author wrote books in their own language, using random formats. Libraries would be in chaos. REST is like a set of library organization guidelines. It says: organize your data into clean 'Resources' (like books), give each resource a unique address (URI), and interact with them using standard actions. This makes APIs clean, predictable, and stateless."*

### Student Engagement Questions
1. *"If we are designing a system for VJTI College, is '/api/getStudentDetails' a good REST URI? Why or why not?"*
2. *"What does 'Stateless' mean in simple terms?"*

### Whiteboard Teaching
```text
  [ Non-RESTful (Verbs) ]           [ RESTful (Nouns) ]
  /api/addNewStudent      ──►       POST /api/students
  /api/deleteStudent?id=5 ──►       DELETE /api/students/5
```

### Teaching Flow
1. Define REST (constraints for web APIs).
2. Explain Statelessness (every request stands alone).
3. Introduce URI modeling (nouns over verbs).

### Common Student Doubts
* **Student:** *"Does stateless mean the server forgets who I am after every click?"*
* **Trainer:** *"Yes! The server doesn't remember your session history. That is why the client must send a token (like an ID card) with *every single* request."*

### Interview Talking Points
* What are the core constraints of REST? (Statelessness, Client-Server, Uniform Interface).
* Why should URIs use nouns instead of verbs?

### Topic Recap
REST models database assets as resource URIs and operates on them statelessy using standard HTTP actions.

### Transition Script
> *"We know REST uses nouns for addresses. But how do we tell the server what action to perform on those nouns? We use HTTP Methods."*

---

## Topic 3: HTTP Methods

### Trainer Introduction
> *"HTTP Methods are verbs that specify the CRUD action we want to take on a resource. It is like telling a librarian: 'I want to Read this book (GET)', 'I want to Add a new book (POST)', or 'I want to Replace this torn book (PUT)'."*

### Student Engagement Questions
1. *"What happens if you run GET 100 times? Does the database content change?"*
2. *"What happens if you submit a POST registration form 3 times due to a slow internet connection?"*

### Whiteboard Teaching
Draw a grid of Safeness vs Idempotency:
```text
  Method   CRUD     Safe?   Idempotent?
  GET      Read     YES     YES
  POST     Create   NO      NO
  PUT      Replace  NO      YES
  DELETE   Delete   NO      YES
```

### Teaching Flow
1. Define CRUD mapping.
2. Differentiate between safe (read-only) and unsafe (write) methods.
3. Explain Idempotency with real-world examples.

### Live Coding Demonstration
#### Step 1: Type
Explain the conceptual verbs mapping:
```javascript
const methodMap = {
  create: 'POST',
  read: 'GET',
  update: 'PUT',
  delete: 'DELETE'
};
```
#### Step 4: Questions
*"If I delete a student with ID 5, and then send a DELETE request for ID 5 again, is it idempotent?"* (Yes, the student is still deleted).

### Common Student Doubts
* **Student:** *"Why is PATCH non-idempotent in some discussions?"*
* **Trainer:** *"Because if PATCH is designed to append a value (like appending a history log), running it twice appends two logs. PUT, however, completely replaces the resource state, making it idempotent."*

### Interview Talking Points
* What is the difference between safe and idempotent HTTP methods?
* Why is POST not idempotent?

### Topic Recap
HTTP Methods declare resource operations, classified by safety and idempotency.

### Transition Script
> *"When we send an HTTP Method action to a resource, how does the server tell us if the action worked? It responds with a Status Code."*

---

## Topic 4: Status Codes

### Trainer Introduction
> *"Think of HTTP status codes as a quick hand signal from a traffic police officer. Instead of explaining a long paragraph, the officer uses a sign. A status code is a 3-digit shorthand answer from the server. 200 series means 'All clear, go ahead!', 400 series means 'You made a mistake!', and 500 series means 'Our system crashed!'"*

### Student Engagement Questions
1. *"Have you ever seen a '404 Page Not Found' error on a website? What does it mean?"*
2. *"If you enter the wrong password, which code group should the server return?"*

### Whiteboard Teaching
Write status code categories on the board:
```text
  1xx: Info
  2xx: Success (200 OK, 201 Created)
  3xx: Redirect
  4xx: Client Error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found)
  5xx: Server Error (500 Internal Server Error)
```

### Teaching Flow
1. Explain the categories (1xx to 5xx).
2. Detail must-know codes (200, 201, 204, 400, 401, 403, 404, 500).
3. Contrast 401 vs 403.

### Common Student Doubts
* **Student:** *"What is the difference between 401 and 403?"*
* **Trainer:** *"Think of a college hostel: 401 means you don't have a hostel ID card at the gate. 403 means you have a hostel ID card, but you are trying to enter the girls' wing when you are not allowed."*

### Topic Recap
Status codes categorize server responses to declare request outcomes quickly.

### Transition Script
> *"Along with status codes, the server sends additional metadata configuration. Let's learn about Headers."*

---

## Topic 5: Headers

### Trainer Introduction
> *"Headers are like the delivery address slip and shipping labels on a Courier package. The package content is the data, but the labels tell the courier: 'Handle with care', 'Contains fragile glass', or 'Requires signature authorization'. Headers manage the configuration of our requests and responses."*

### Student Engagement Questions
1. *"How does a server know if the data we are sending is JSON or XML?"*
2. *"How do we send security passwords or tokens securely without displaying them in the URL?"*

### Whiteboard Teaching
Draw a request packet with header and body separation:
```text
  +---------------------------------------+
  | HEADERS (Metadata)                    |
  | - Content-Type: application/json     |
  | - Authorization: Bearer token123      |
  +---------------------------------------+
  | BODY (Payload)                        |
  | { "rollNo": 1001, "name": "Rahul" }   |
  +---------------------------------------+
```

### Teaching Flow
1. Define headers as metadata.
2. Differentiate between Request Headers and Response Headers.
3. Detail common headers like `Content-Type` and `Authorization`.

### Common Student Doubts
* **Student:** *"Do I need to create headers manually for every request?"*
* **Trainer:** *"Most UI clients and Playwright automatically generate basic headers. However, for security tokens and custom payload formats, we configure them manually in our test setup."*

### Topic Recap
Headers provide metadata configuration, such as payload formatting and auth keys.

### Transition Script
> *"Sometimes, we don't want to send a full payload. We just want to filter or search for records. We do this using Query Parameters."*

---

## Topic 6: Query Parameters

### Trainer Introduction
> *"Imagine looking at a student registry file. If you want to see all students, you open the registry. If you only want to see students who belong to 'Computer Science' and have unpaid fees, you filter the table columns. Query parameters are filters that we add to the end of a URL to narrow down the server's output."*

### Student Engagement Questions
1. *"Where does the query parameters list start in a URL? What character is used?"*
2. *"If we filter by branch=CS and status=Active, how do we chain these filters together?"*

### Whiteboard Teaching
```text
  URL: https://api.vjti.edu/students?branch=CS&feesPaid=false
                                    ▲        ▲
                              Query Start  Chain Parameter
```

### Teaching Flow
1. Explain query parameter syntax (`?` and `&`).
2. Demonstrate how query parameters are appended to GET requests.
3. Discuss security rules (never send passwords in query parameters).

### Topic Recap
Query parameters filter resources by appending key-value filters to URLs.

### Transition Script
> *"For simple searches, query parameters in URLs work great. But what if we want to submit large amounts of data, like a complete registration form? We use a Request Body."*

---

## Topic 7: Request Body

### Trainer Introduction
> *"If query parameters are like writing a brief note on the outside of an envelope, the Request Body is the main letter stuffed inside the envelope. It holds the core payload containing structured data (typically JSON) that we want the server to insert into our database."*

### Student Engagement Questions
1. *"Does a GET request need a request body? Why or why not?"*
2. *"What format is most commonly used for request bodies in modern APIs?"*

### Whiteboard Teaching
```text
  [ Client JavaScript Object ] ──► JSON.stringify() ──► [ Raw JSON Text Payload ]
  { studentName: "Rahul" }        (Serialization)        '{"studentName":"Rahul"}'
```

### Teaching Flow
1. Define request body as payload.
2. Map body usage to POST/PUT/PATCH methods.
3. Explain JSON formatting rules (double quotes, key-value structure).

### Topic Recap
The request body contains the main payload for creating/editing resources.

### Transition Script
> *"Now we understand all the parts of a request and response. Let's look at how we validate these responses in Playwright: Response Validation."*

---

## Topic 8: Response Validation

### Trainer Introduction
> *"API testing is all about validation. In UI tests, we check if a button is visible. In API tests, we check if the status code is correct, the headers match expectations, and the JSON data payload matches our requirements."*

### Whiteboard Teaching
List the Validation Checklist:
1. Status Code Assertion (`toBe(200)`)
2. Response Time Check ($< 500\text{ms}$)
3. Header Assertions (`Content-Type` verification)
4. Body Data Validation (check properties and values)

### Teaching Flow
1. Detail the validation checklist.
2. Explain why checking status code first prevents crashes during test runs.
3. Introduce Playwright validation matches.

### Transition Script
> *"Let's put this validation theory into practice. Let's write our first Playwright API test for the GET method."*

---

## Topic 9: GET Method

### Trainer Introduction
> *"Let's write a Playwright test to execute a GET request. We will use Playwright's built-in 'request' fixture, which is pre-configured to make network calls directly without loading a browser."*

### Teaching Flow
1. Open code editor and showcase `api.spec.ts`.
2. Write a GET test.
3. Assert status and JSON content.

### Live Coding Demonstration
#### Step 1: Type
```javascript
import { test, expect } from '@playwright/test';

test('Get User Profile Verification', async ({ request }) => {
  const response = await request.get('https://jsonplaceholder.typicode.com/users/1');
  expect(response.status()).toBe(200);
  
  const body = await response.json();
  expect(body.id).toBe(1);
  expect(body.name).toBe('Leanne Graham');
});
```
#### Step 2: Explain
* `{ request }`: Built-in fixture representing the API client.
* `request.get()`: Sends the HTTP GET call.
* `expect(body.id).toBe(1)`: Validates that the correct user profile was retrieved.
#### Step 3: Expected Output
```text
  ✓ Get User Profile Verification (Pass)
```
#### Step 5: Mini Exercise
Ask students to add an assertion to verify that the email property in the response contains `"Sincere@april.biz"`.

### Topic Recap
GET reads server data and is validated using status and body checks in Playwright.

### Transition Script
> *"Reading data is easy. Now, let's learn how to send a payload to create a new resource using the POST method."*

---

## Topic 10: POST Method

### Trainer Introduction
> *"When registering a student, we send a POST request with the student's details in the request body. Playwright makes this easy: we just pass a JSON object inside the 'data' key, and Playwright handles the serialization and headers automatically."*

### Teaching Flow
1. Introduce POST method and request body syntax in Playwright.
2. Live code POST validation.
3. Assert status is `201 Created`.

### Live Coding Demonstration
#### Step 1: Type
```javascript
import { test, expect } from '@playwright/test';

test('Register New Post Verification', async ({ request }) => {
  const response = await request.post('https://jsonplaceholder.typicode.com/posts', {
    data: {
      title: 'VJTIBootcamp',
      body: 'API Testing Fundamentals',
      userId: 10
    }
  });

  expect(response.status()).toBe(201);
  
  const body = await response.json();
  expect(body.id).toBeDefined();
  expect(body.title).toBe('VJTIBootcamp');
});
```
#### Step 2: Explain
* `data`: Contains the payload object to serialize.
* `toBe(201)`: Validates creation success.
* `toBeDefined()`: Validates that the server generated an ID for the new item.
#### Step 3: Expected Output
```text
  ✓ Register New Post Verification (Pass)
```
#### Step 5: Mini Exercise
Have students send a POST request with their name as the title, and assert that the name matches in the response body.

### Topic Recap
POST creates resources using payloads, returning 201 status codes.

### Transition Script
> *"Once a resource is created, we might need to modify it. Let's study how to replace an entire record using the PUT method."*

---

## Topic 11: PUT Method

### Trainer Introduction
> *"PUT replaces the entire resource. If the database has a record with name, email, and branch, and we send a PUT request with only the name, the email and branch will be set to null or deleted. Let's see how this works in code."*

### Teaching Flow
1. Write a PUT automation block in Playwright.
2. Assert status is 200 OK.
3. Validate modified fields.

### Live Coding Demonstration
#### Step 1: Type
```javascript
import { test, expect } from '@playwright/test';

test('Replace Post Verification', async ({ request }) => {
  const response = await request.put('https://jsonplaceholder.typicode.com/posts/1', {
    data: {
      id: 1,
      title: 'Updated Title',
      body: 'Updated Body Content',
      userId: 1
    }
  });

  expect(response.status()).toBe(200);
  
  const body = await response.json();
  expect(body.title).toBe('Updated Title');
});
```
#### Step 3: Expected Output
```text
  ✓ Replace Post Verification (Pass)
```

### Topic Recap
PUT replaces resources entirely, returning 200 OK on success.

### Transition Script
> *"If we don't want to replace everything and only want to update a single field, we use PATCH instead of PUT."*

---

## Topic 12: PATCH Method

### Trainer Introduction
> *"PATCH applies partial updates. We only send the fields we want to change, saving network bandwidth. Other fields remain untouched."*

### Teaching Flow
1. Write a PATCH code block.
2. Contrast with PUT payload size.

### Live Coding Demonstration
#### Step 1: Type
```javascript
import { test, expect } from '@playwright/test';

test('Partial Update Post Verification', async ({ request }) => {
  const response = await request.patch('https://jsonplaceholder.typicode.com/posts/1', {
    data: {
      title: 'Only Title Updated'
    }
  });

  expect(response.status()).toBe(200);
  
  const body = await response.json();
  expect(body.title).toBe('Only Title Updated');
  expect(body.body).toBeDefined(); // Remaining fields are still present
});
```
#### Step 3: Expected Output
```text
  ✓ Partial Update Post Verification (Pass)
```

### Topic Recap
PATCH updates specific resource fields partially.

### Transition Script
> *"Finally, let's look at how we clean up and delete records from our server using the DELETE method."*

---

## Topic 13: DELETE Method

### Trainer Introduction
> *"DELETE removes a resource from the server. It is idempotent: the first request deletes the data, and subsequent calls keep the data deleted. It usually returns 200 OK or 204 No Content."*

### Teaching Flow
1. Write a DELETE test.
2. Validate 200/204 status ranges.

### Live Coding Demonstration
#### Step 1: Type
```javascript
import { test, expect } from '@playwright/test';

test('Delete Post Verification', async ({ request }) => {
  const response = await request.delete('https://jsonplaceholder.typicode.com/posts/1');
  
  expect([200, 204]).toContain(response.status());
});
```
#### Step 2: Explain
We send a delete request. Since some servers return 200 and others return 204, we use `toContain()` to make our test robust.
#### Step 3: Expected Output
```text
  ✓ Delete Post Verification (Pass)
```

### Topic Recap
DELETE removes resources, returning success codes like 200 or 204.

---

## Session Closing

### Session Summary
* We covered API fundamentals and the client-server request-response model.
* We explored REST architectural guidelines (nouns in URIs, statelessness).
* We mapped CRUD operations to GET, POST, PUT, PATCH, and DELETE.
* We analyzed status codes (2xx success, 4xx client errors, 5xx server errors).
* We wrote automated API test validations in Playwright verifying status, headers, and body payloads.

### Knowledge Check Questions
1. *"What is the difference between safe and idempotent HTTP methods?"*
2. *"Which status code is returned when a resource is successfully created?"*
3. *"Why does missing 'Content-Type' crash JSON parsing on a server?"*
4. *"Does a GET request contain a request body?"*
5. *"Why is PATCH preferred over PUT when updating a single student column in a database?"*

### Assignment Introduction
Explain that the assignments reinforce these CRUD automation scripts. Students will write test suites validating GET, POST, and DELETE calls against live mock endpoints, matching standard QA interview patterns.

### Homework Guidance
Students should practice creating tests locally inside the `tests/` directory of their projects. Advise them to use mock endpoints (like JSONPlaceholder) to build confidence with assertions.

### Next Session Preview
In the next session (Day 12: Advanced API Testing), we will learn how to handle API security tokens, extract data dynamically, chain multiple APIs together (e.g. login $\rightarrow$ extract token $\rightarrow$ create student), and manage environment variables.
