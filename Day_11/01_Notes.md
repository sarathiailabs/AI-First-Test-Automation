# Day 11: API Testing Fundamentals

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| API Fundamentals | 10 mins |
| REST Architecture | 10 mins |
| HTTP Methods | 10 mins |
| Status Codes | 10 mins |
| Headers | 10 mins |
| Query Parameters | 10 mins |
| Request Body | 10 mins |
| Response Validation | 15 mins |
| GET Method | 10 mins |
| POST Method | 10 mins |
| PUT Method | 10 mins |
| PATCH Method | 10 mins |
| DELETE Method | 5 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Explain what an API is and identify its core components (Request, Response, Client, Server).
* Understand the rules of REST Architecture and how resources are modeled.
* Differentiate between safe, unsafe, idempotent, and non-idempotent HTTP methods.
* Correctly interpret HTTP status code ranges (2xx, 3xx, 4xx, 5xx) during testing.
* Inspect and construct Headers, Query Parameters, and Request Bodies.
* Validate API responses (status codes, headers, and body payloads) in Playwright tests.
* Write automated API tests for CRUD operations (GET, POST, PUT, PATCH, DELETE) using Playwright.

---

## Introduction

In the previous sessions, we focused entirely on automating the User Interface (UI). Today, we shift our focus to API Testing. APIs are the backbone of modern web applications. Before data is rendered on a browser screen, it is retrieved from databases using APIs. API testing is faster, more reliable, and helps catch bugs early in the development lifecycle before the UI is even built.

---

## API Fundamentals

### Definition
An **API (Application Programming Interface)** is a software intermediary that allows two distinct applications to communicate and share data with each other. *(API ek bridge ya messenger ki tarah hai jo do software applications ko aapas me baat karne aur data share karne ki permission deta hai).*

### Key Concepts
* **Client:** The application that sends the request (e.g., a mobile app, browser UI, or automation script).
* **Server:** The computer system that processes the request, interacts with the database, and returns the result.
* **Request:** The message sent by the client containing instructions and data.
* **Response:** The message sent back by the server containing the result (or error).
* **Speed & Reliability:** API tests run in milliseconds because they do not require loading heavy HTML/CSS/JS code in a browser.

### Visual Explanation
**The Restaurant Analogy:**
Imagine you are dining at a local restaurant.
1. **You (Client):** Sit at the table and look at the menu.
2. **Waiter (API):** Takes your order (Request) and walks to the kitchen.
3. **Kitchen (Server):** Prepares your food (Processes Data) and hands it to the Waiter.
4. **Waiter (API):** Delivers the hot food (Response) back to your table.

Without the waiter, you would have to go to the kitchen yourself. Similarly, the API acts as the waiter between the frontend UI and backend database.

### Example
#### Code
```javascript
// Simple demonstration of fetching a student's profile from an API using browser fetch
fetch('https://api.vjti.edu/students/1001')
  .then(response => response.json())
  .then(data => console.log(data));
```
#### Output
```json
{
  "rollNo": 1001,
  "studentName": "Rahul Sharma",
  "branch": "Computer Science"
}
```
#### Explanation
1. The client triggers a request to `https://api.vjti.edu/students/1001`.
2. The VJTI server processes the ID `1001` and retrieves student details from the database.
3. The server responds with a JSON payload containing Rahul Sharma's information.

### Real World Usage
When you pay using UPI (GPay/PhonePe), the app calls an API of the bank server to deduct money and send it to the receiver. The app itself doesn't have direct access to bank accounts; it relies entirely on secure APIs.

### Common Mistakes
* **Thinking APIs and UI are the same:** The UI is what the user *sees*. The API is how the data is *fetched*. An API has no buttons, colors, or fonts; it deals purely with raw data (usually JSON).

### Topic Summary
An API enables system-to-system communication using a Request-Response model without involving visual user interfaces.

---

## REST Architecture

### Definition
**REST (Representational State Transfer)** is an architectural style that defines a set of constraints and guidelines for creating scalable and stateless web services over HTTP. *(REST ek simple rules ka group hai jise follow karke web services (APIs) banayi jaati hain taaki wo reliable aur speed me run karein).*

### Key Concepts
* **Statelessness:** Each request from a client must contain all the information needed to understand and process it. The server does not store any session history about the client.
* **Client-Server Separation:** The client (frontend UI) and the server (backend database) are completely independent. Changing the UI design does not affect the database schema.
* **Resources:** Everything is treated as a "Resource" and identified by a unique URL (URI).
* **Uniform Interface:** Standardized endpoints and HTTP methods are used globally.

| UI Action | API Resource Path | HTTP Method |
| --- | --- | --- |
| View all students | `/api/students` | GET |
| Add a student | `/api/students` | POST |
| Update student details | `/api/students/1001` | PUT |
| Remove student | `/api/students/1001` | DELETE |

### Example
#### Code
```javascript
// RESTful URI Structure Example
// Good REST endpoint representing a collection of student resources
const endpointAll = "/api/students";

// Good REST endpoint representing a specific student resource by ID
const endpointSingle = "/api/students/1001";
```
#### Explanation
REST resources use nouns (`students`) instead of verbs (`getStudent`). The action (get, add, delete) is defined by the HTTP method, not the URL path.

### Real World Usage
E-commerce websites like Flipkart use REST APIs. When you click on a product, your browser calls `/api/products/abc-123` to fetch product specs, reviews, and stock.

### Common Mistakes
* **Using verbs in URI paths:** Creating endpoints like `/api/getAllStudents` or `/api/deleteStudent?id=10`. In REST, these should simply be GET `/api/students` and DELETE `/api/students/10`.

### Topic Summary
REST APIs model data as resources identified by URI paths and interact with them using standard HTTP methods under stateless execution.

---

## HTTP Methods

### Definition
**HTTP Methods** (also known as HTTP Verbs) indicate the desired action to be performed on a target resource on the server. *(HTTP methods server ko ye batate hain ki target data ke upar kaunsa action (jaise add, update, delete ya read) perform karna hai).*

### Key Concepts
* **CRUD Operations:** Map directly to HTTP methods.
  * **C**reate $\rightarrow$ **POST**
  * **R**ead $\rightarrow$ **GET**
  * **U**pdate $\rightarrow$ **PUT / PATCH**
  * **D**elete $\rightarrow$ **DELETE**
* **Safe Methods:** Methods that do not modify database resources (e.g., GET).
* **Idempotent Methods:** Methods that produce the same result no matter how many times they are executed (e.g., GET, PUT, DELETE). Running them 1 time or 100 times leaves the server state identical. POST is NOT idempotent because calling it multiple times creates duplicate records.

| Method | CRUD | Safe? | Idempotent? |
| --- | --- | --- | --- |
| **GET** | Read | Yes | Yes |
| **POST** | Create | No | No |
| **PUT** | Update (Replace) | No | Yes |
| **PATCH** | Update (Partial) | No | No |
| **DELETE** | Delete | No | Yes |

### Example
#### Code
```javascript
// HTTP Methods mapping
const methods = {
  fetchData: "GET",       // Safe & Idempotent
  createRecord: "POST",   // Unsafe & Non-Idempotent
  replaceRecord: "PUT",   // Unsafe & Idempotent
  modifyRecord: "PATCH",  // Unsafe & Non-Idempotent
  removeRecord: "DELETE"  // Unsafe & Idempotent
};
```
#### Explanation
Each method has designated properties. An automated test must verify that unsafe methods (like POST) actually modify the state, while safe methods (like GET) read it without changes.

### Common Mistakes
* **Using GET to update data:** Using `GET /api/delete-student?id=5`. If a browser pre-fetches the page link, it could accidentally delete the student record. Always use the correct verb (`DELETE`).

### Topic Summary
HTTP methods define structural CRUD operations on resources, classified by safeness and idempotency parameters.

---

## Status Codes

### Definition
**HTTP Status Codes** are 3-digit standardized response codes returned by the server to inform the client about the success or failure of their request. *(Status codes server dwara diye gaye 3-digit ke answers hain jo batate hain ki request pass hui ya fail).*

### Key Concepts
Status codes are divided into five categories based on their first digit:
* **1xx (Informational):** Request received, continuing process (e.g., 100 Continue).
* **2xx (Success):** The action was successfully received, understood, and accepted (e.g., 200 OK, 201 Created).
* **3xx (Redirection):** Further action needs to be taken to complete the request (e.g., 301 Moved Permanently).
* **4xx (Client Error):** The request contains bad syntax or cannot be fulfilled (e.g., 400 Bad Request, 401 Unauthorized, 404 Not Found).
* **5xx (Server Error):** The server failed to fulfill an apparently valid request (e.g., 500 Internal Server Error, 503 Service Unavailable).

#### Key Status Codes to Memorize for Interviews:
* **200 OK:** Successful GET request.
* **201 Created:** Successful POST request (new resource created).
* **204 No Content:** Successful request but no data returned in the response body (common in DELETE).
* **400 Bad Request:** Server cannot process the request due to client validation error (e.g., missing mandatory field).
* **401 Unauthorized:** Authentication is missing or invalid (e.g., invalid token).
* **403 Forbidden:** Authenticated but does not have permission to access the resource.
* **404 Not Found:** Endpoint or resource does not exist.
* **500 Internal Server Error:** Backend code crashed.

### Example
#### Code
```javascript
// Simulated status check block
const responseCode = 201;

if (responseCode === 201) {
  console.log("Resource created successfully!");
} else if (responseCode === 404) {
  console.error("Resource does not exist.");
}
```
#### Output
```text
Resource created successfully!
```

### Common Mistakes
* **Confusing 401 and 403:**
  * **401 Unauthorized** means *"I don't know who you are (No valid ID card)"*.
  * **403 Forbidden** means *"I know who you are, but you are not allowed to enter this room (You are a student, but this is the Principal's cabin)"*.

### Topic Summary
HTTP status codes categorize response states into ranges: success (2xx), redirection (3xx), client-side failures (4xx), and server crashes (5xx).

---

## Headers

### Definition
**HTTP Headers** are key-value pairs sent alongside HTTP requests and responses to provide metadata (extra configuration and context) about the transaction. *(Headers extra details ke tags hote hain jo client aur server aapas me configuration aur security metadata exchange karne ke liye use karte hain).*

### Key Concepts
* **Request Headers:** Sent by client.
  * `Content-Type`: Tells the server the format of the request body (e.g., `application/json`).
  * `Accept`: Tells the server what format the client wants the response in.
  * `Authorization`: Used to pass security tokens (e.g., Bearer tokens).
* **Response Headers:** Sent by server.
  * `Content-Type`: Tells the client the format of the returned data.
  * `Date`: Time the response was generated.
  * `Server`: Information about the server hosting the API.

### Example
#### Code
```javascript
// Header definitions in a request structure
const requestOptions = {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer my-secret-token'
  }
};
```
#### Explanation
This metadata tells the server that the request is carrying JSON data and provides a secret security token to authorize the transaction.

### Common Mistakes
* **Forgetting to set Content-Type:** Sending a JSON string payload in a POST request without setting `'Content-Type': 'application/json'`. The server might treat it as plain text and fail to parse it, resulting in a `400 Bad Request` or `500 Internal Server Error`.

### Topic Summary
Headers convey essential transaction metadata, such as payload data formats and security tokens, between client and server.

---

## Query Parameters

### Definition
**Query Parameters** are optional key-value pairs appended to the end of a URL to filter, sort, or paginate database results. *(Query parameters URL ke end me query sign (?) ke baad lagaye jaane wale filter parameters hain).*

### Key Concepts
* **Syntax:** `?key=value` at the end of the URL. Multiple parameters are combined using `&` (e.g., `?branch=CS&gender=female`).
* **Usage:** Used inside GET requests. They do NOT change the API endpoint; they only filter the output returning from that endpoint.
* **Security Warning:** Query parameters are visible in browser history and server logs. Never pass passwords or secret tokens inside query parameters.

### Example
#### Code
```javascript
// Filter VJTI students belonging to the CS branch who have unpaid fees
const targetURL = "https://api.vjti.edu/students?branch=CS&feesPaid=false";
console.log(`Querying URL: ${targetURL}`);
```
#### Output
```text
Querying URL: https://api.vjti.edu/students?branch=CS&feesPaid=false
```
#### Explanation
The request hits the general `/students` endpoint, but the server applies database filters to only return students who belong to `CS` AND have `feesPaid` set to `false`.

### Real World Usage
When searching for shoes on Amazon and filtering by "Adidas" and size "8", the URL query parameters look like `?brand=Adidas&size=8`.

### Common Mistakes
* **Using wrong characters:** Using `&` instead of `?` to start query parameters, or vice versa (e.g. `/api/students&branch=CS`). The first parameter *must* start with `?`, and subsequent parameters *must* start with `&`.

### Topic Summary
Query parameters filter database results dynamically via URL arguments, starting with `?` and chained with `&`.

---

## Request Body

### Definition
The **Request Body** (Payload) is the raw data block sent by the client to the server inside the body of an HTTP request (typically POST, PUT, or PATCH) to create or update resources. *(Request Body wo main content data packet hai jo client server ko bheja hai naya record database me create ya edit karne ke liye).*

### Key Concepts
* **Methods:** Mainly used with write operations (POST, PUT, PATCH). GET requests typically do not have a body.
* **Formats:** The most common format is JSON (JavaScript Object Notation), though XML or Form-Data are also supported.
* **JSON Syntax:** Objects are enclosed in curly braces `{}` and use key-value pairs with double quotes `"`.

### Example
#### Code
```javascript
// Student details payload formatted in JSON string
const studentData = {
  name: "Amit Patel",
  age: 20,
  branch: "IT"
};

// Convert Javascript object to JSON string before transmission
const jsonBody = JSON.stringify(studentData);
console.log(jsonBody);
```
#### Output
```json
{"name":"Amit Patel","age":20,"branch":"IT"}
```

### Common Mistakes
* **Sending JSON without parsing/stringifying:** Passing raw JavaScript objects directly to API requests without converting them to a JSON string, or forgetting double quotes in manual JSON payloads.

### Topic Summary
The request body carries the data packet required by the server to create or modify records, generally formatted as a JSON string.

---

## Response Validation

### Definition
**Response Validation** is the core testing activity of comparing an API's returned response parameters against expected requirements to confirm system correctness. *(Response validation ka matlab hai server dwara bheje gaye data ko verify karna ki status, headers aur body correct hain ya nahi).*

### Key Concepts
During validation, we write assertions to verify:
1. **Status Code:** Is it 200 (Success) or 201 (Created) as expected?
2. **Response Time:** Did the API respond within SLA limits (e.g., $< 500\text{ms}$)?
3. **Response Headers:** Check `Content-Type` is `application/json`.
4. **Response Body Data:** Check specific data fields, arrays, and value types.

### Example
#### Code
```javascript
const response = {
  status: 200,
  headers: { 'content-type': 'application/json' },
  body: { id: 1005, status: "Active" }
};

// Simulated Automated Test Assertions
console.assert(response.status === 200, "Validation Failed: Status Code mismatch!");
console.assert(response.headers['content-type'] === 'application/json', "Validation Failed: Header mismatch!");
console.assert(response.body.status === "Active", "Validation Failed: Field value mismatch!");
console.log("All validations passed!");
```
#### Output
```text
All validations passed!
```

### Topic Summary
API response validation checks status codes, header configurations, and body payload structures to confirm backend functionality.

---

## GET Method

### Definition
The HTTP **GET** method is used to retrieve data from a server. It does not modify any database records. *(GET method ka use database se data padhne (read karne) ke liye kiya jata hai).*

### Key Concepts
* Safe and Idempotent.
* Can have query parameters for filtering.
* Response contains a status code (usually 200 OK) and the requested resource data.

### Example
#### Code
```javascript
import { test, expect } from '@playwright/test';

test('GET VJTI Student Details API Validation', async ({ request }) => {
  // Execute GET request using Playwright APIRequestContext
  const response = await request.get('https://jsonplaceholder.typicode.com/users/1');
  
  // Assert status code is 200 OK
  expect(response.status()).toBe(200);
  
  // Parse response body as JSON
  const body = await response.json();
  
  // Validate student details
  expect(body.id).toBe(1);
  expect(body.name).toBeDefined();
  expect(body.email).toContain('@');
});
```
#### Output
```text
  GET VJTI Student Details API Validation (Pass - 150ms)
```
#### Explanation
Using Playwright's `request` fixture, we send a GET call to retrieve user data. We assert the response status is exactly 200, parse the response payload, and assert that the name exists and the email contains the `@` symbol.

### Real World Usage
Every time you open your college web portal and view your profile info, the page triggers a GET request behind the scenes to fetch your details.

### Common Mistakes
* **Sending sensitive data like passwords:** Since GET parameters appear in URL logs, sending passwords via GET exposes secrets. Never use GET for login authentication payload transmission.

### Topic Summary
The GET method reads server data safely and is automated in Playwright using `request.get()`, verifying status and body arrays.

---

## POST Method

### Definition
The HTTP **POST** method is used to send data to the server to create a new resource. *(POST method ka use server par naya data record banane ke liye kiya jata hai).*

### Key Concepts
* Unsafe and Non-Idempotent. Calling POST twice will create two separate records in the database.
* Requires a Request Body (data packet to create).
* Returns status `201 Created` on successful creation.

### Example
#### Code
```javascript
import { test, expect } from '@playwright/test';

test('POST Register Student API Validation', async ({ request }) => {
  // Trigger POST request with JSON payload
  const response = await request.post('https://jsonplaceholder.typicode.com/users', {
    data: {
      name: "Suresh Patel",
      username: "suresh.vjti",
      email: "suresh@vjti.edu"
    }
  });

  // Assert status is 201 Created
  expect(response.status()).toBe(201);

  const body = await response.json();
  
  // Verify server database appended and assigned a new unique ID
  expect(body.id).toBeDefined();
  expect(body.name).toBe("Suresh Patel");
});
```
#### Output
```text
  POST Register Student API Validation (Pass - 180ms)
```
#### Explanation
We send a POST call along with a `data` configuration block. Playwright automatically stringifies the object and sets the `Content-Type` header to `application/json`. We assert the status is `201` and verify the server generated a fresh resource ID.

### Real World Usage
When you click "Submit" on a college admission form or register a new account on Amazon, the app fires a POST API request to add your data to the database.

### Common Mistakes
* **Using wrong success assertions:** Expecting `200 OK` for all creations. Standard REST rules require `201 Created` for new record additions. Check your API documentation first.

### Topic Summary
POST creates new server records using data payloads, verified by asserting response keys and `201` statuses.

---

## PUT Method

### Definition
The HTTP **PUT** method is used to update an existing resource by completely replacing it with a new payload. *(PUT method ka use existing record ko naye values ke sath poorna roop se badalne (completely replace karne) ke liye kiya jata hai).*

### Key Concepts
* Unsafe but Idempotent. Sending the same replacement payload 5 times will result in the exact same state as sending it 1 time.
* If the resource does not exist, PUT can either create a new resource or throw an error (depending on system design).
* Replaces the entire resource. Any omitted fields will be set to null or default values.

### Example
#### Code
```javascript
import { test, expect } from '@playwright/test';

test('PUT Complete Profile Update API Validation', async ({ request }) => {
  // Update entire student profile with new details
  const response = await request.put('https://jsonplaceholder.typicode.com/users/1', {
    data: {
      name: "Rahul R. Sharma",
      email: "rahul.new@vjti.edu",
      phone: "9876543210"
    }
  });

  // Verify successful update (200 OK)
  expect(response.status()).toBe(200);

  const body = await response.json();
  expect(body.name).toBe("Rahul R. Sharma");
  expect(body.email).toBe("rahul.new@vjti.edu");
});
```
#### Output
```text
  PUT Complete Profile Update API Validation (Pass - 145ms)
```

### Common Mistakes
* **Omiting optional fields in PUT:** Since PUT replaces the *entire* resource, if you only send `name` and omit the existing `address`, the server might erase the address or set it to `null`. For partial updates, use PATCH instead.

### Topic Summary
PUT updates a resource by replacing it entirely, requiring a complete payload, returning `200 OK` on success.

---

## PATCH Method

### Definition
The HTTP **PATCH** method is used to apply partial modifications to an existing resource. *(PATCH method ka use database me kisi record ke specific columns ya fields ko partially update karne ke liye hota hai).*

### Key Concepts
* Unsafe and Non-Idempotent (depends on implementation, e.g., appending values to arrays is non-idempotent).
* Saves bandwidth because you only send the fields you want to change, not the entire resource.
* Returns `200 OK` on successful update.

### Example
#### Code
```javascript
import { test, expect } from '@playwright/test';

test('PATCH Update Email Only API Validation', async ({ request }) => {
  // Update ONLY the email field of the student, keeping other profile fields intact
  const response = await request.patch('https://jsonplaceholder.typicode.com/users/1', {
    data: {
      email: "rahul.vjti@gmail.com"
    }
  });

  expect(response.status()).toBe(200);

  const body = await response.json();
  expect(body.email).toBe("rahul.vjti@gmail.com");
  // Other fields like name and phone remain unchanged on the server
});
```
#### Output
```text
  PATCH Update Email Only API Validation (Pass - 130ms)
```

### Real World Usage
Updating your password inside profile settings. You do not re-send your profile picture and address; the client fires a PATCH request containing only the new password.

### Topic Summary
PATCH updates specific resource fields partially, saving bandwidth compared to complete PUT replacements.

---

## DELETE Method

### Definition
The HTTP **DELETE** method is used to delete a specific resource from the server database. *(DELETE method ka use server database se kisi record ko humesha ke liye hatane ke liye kiya jata hai).*

### Key Concepts
* Unsafe but Idempotent. The first call deletes the resource. Subsequent calls will still result in the resource being deleted (though status codes might shift from `200/204` to `404 Not Found`).
* Usually does not require a request body. The resource to delete is identified via the URL path.
* Returns `200 OK` or `204 No Content` (success with no response body).

### Example
#### Code
```javascript
import { test, expect } from '@playwright/test';

test('DELETE Student Record API Validation', async ({ request }) => {
  // Delete student with ID 1
  const response = await request.delete('https://jsonplaceholder.typicode.com/users/1');

  // Assert response status is 200 or 204
  expect([200, 204]).toContain(response.status());
});
```
#### Output
```text
  DELETE Student Record API Validation (Pass - 110ms)
```
#### Explanation
The DELETE request is sent to user path `1`. Playwright validates the server responded with either `200` or `204` status, indicating successful deletion.

### Common Mistakes
* **Expecting 200 OK only:** Many modern RESTful APIs return `204 No Content` upon successful deletion because there is no data left to return. Write assertions that accommodate both `200` and `204`.

### Topic Summary
DELETE removes resources by identifier URL, returning success indicators such as `200` or `204` status codes.

---

## Session Summary

### Key Takeaways
1. **API Role:** Acts as an intermediary bridge handling request-response exchanges.
2. **REST Constraints:** Models data as resources under stateless guidelines.
3. **Methods Partitioning:** GET is safe; POST creates; PUT replaces; PATCH modifies partially; DELETE removes.
4. **Status Meanings:** `2xx` indicates success, `4xx` client failures, and `5xx` server faults.
5. **Request Assets:** Headers supply metadata, query parameters filter GET requests, and bodies supply write data payloads.

### Important Interview Points
* **What is the difference between safe and idempotent HTTP methods?**
  * Safe methods do not modify server resources (e.g. GET). Idempotent methods produce the same server state regardless of whether they are run 1 time or 100 times (e.g. GET, PUT, DELETE). POST is neither safe nor idempotent.
* **Explain the difference between 401 and 403 status codes.**
  * `401 Unauthorized` means authentication is missing or invalid (the client identity is unknown). `403 Forbidden` means identity is known, but the client does not have authorization rights to access the resource.
* **When would you choose PATCH over PUT?**
  * Use PUT when you want to replace an entire resource payload. Use PATCH when you only want to modify a few fields of a resource to save network bandwidth.
* **How does Playwright handle API requests in test scripts?**
  * Playwright provides a built-in `request` context fixture that lets you execute HTTP calls (`request.get()`, `request.post()`, etc.) directly inside test blocks without needing external libraries like Axios.

### Quick Revision Sheet

| Component | Role | Playwright API Code Pattern | Typical Status |
| --- | --- | --- | --- |
| **GET** | Read Resource | `request.get('/api/users/1')` | 200 OK |
| **POST** | Create Resource | `request.post('/api/users', { data })` | 201 Created |
| **PUT** | Replace Resource | `request.put('/api/users/1', { data })` | 200 OK |
| **PATCH** | Update Part | `request.patch('/api/users/1', { data })` | 200 OK |
| **DELETE**| Remove Resource | `request.delete('/api/users/1')` | 200 / 204 |
| **Headers**| Supply Metadata | `{ headers: { 'Content-Type': 'json' } }` | N/A |
| **Params** | Filter Output | `request.get('/api/users?branch=CS')` | N/A |
