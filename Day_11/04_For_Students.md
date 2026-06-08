# Day 11: API Testing Fundamentals – Student Revision Notes

This sheet is a quick-revision companion to review the definitions, code syntax, key takeaways, and common mistakes for all the topics covered in Day 11.

---

## Topic Revision

### 1. API Fundamentals
* **Definition:** An API is a software link allowing two systems to communicate. *(API ek bridge hai jo do programs ko aapas me data share karne deta hai).*
* **Example:**
  ```javascript
  // Get student ID 1001 data
  fetch('https://api.vjti.edu/students/1001')
  ```
* **Key Points:**
  * Client sends requests, Server returns responses.
  * Faster than UI testing because it loads raw data, not web pages.
* **Common Mistakes:** Thinking that API testing requires opening a browser window. It doesn't.

---

### 2. REST Architecture
* **Definition:** A standardized set of design guidelines for creating scalable and stateless web services. *(REST rules ka ek collection hai jo network APIs ko systematic aur lightweight banata hai).*
* **Example:**
  * URIs should use nouns: `/api/students` (Good), instead of `/api/getStudents` (Bad).
* **Key Points:**
  * Stateless: Server doesn't store user session history.
  * Client and server are fully independent.
* **Common Mistakes:** Putting action verbs inside the URL (e.g. `/api/deleteUser`). Use HTTP verbs instead.

---

### 3. HTTP Methods
* **Definition:** Standard verbs indicating the CRUD action to perform on a resource. *(HTTP methods server ko batate hain ki target record ke sath kya operation karna hai).*
* **Example:**
  * GET $\rightarrow$ Read, POST $\rightarrow$ Create, PUT $\rightarrow$ Replace, PATCH $\rightarrow$ Partial Edit, DELETE $\rightarrow$ Remove.
* **Key Points:**
  * Safe methods (GET) do not modify data.
  * Idempotent methods (GET, PUT, DELETE) leave the server in the same state if called multiple times.
* **Common Mistakes:** Using a GET method to add or delete database rows.

---

### 4. Status Codes
* **Definition:** 3-digit numeric codes indicating the outcome of an HTTP request. *(Status codes server ka quick answer hote hain jo request ka success/fail status batate hain).*
* **Key Points:**
  * `200 OK` (GET success), `201 Created` (POST success), `204 No Content` (DELETE success).
  * `4xx` $\rightarrow$ Client Errors (e.g., 400 Bad Request, 404 Not Found).
  * `5xx` $\rightarrow$ Server Errors (e.g., 500 Internal Server Error).
* **Common Mistakes:** Confusing 401 (identity unknown) with 403 (identity known but access denied).

---

### 5. Headers
* **Definition:** Key-value pairs containing metadata and configuration parameters. *(Headers extra details ke labels hain jo client-server configuration exchange karte hain).*
* **Example:**
  ```javascript
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer my-token'
  }
  ```
* **Key Points:**
  * `Content-Type` defines the data format of the body (e.g., JSON).
  * `Authorization` passes secure access keys.
* **Common Mistakes:** Forgetting to send `'Content-Type': 'application/json'` when sending a JSON body payload.

---

### 6. Query Parameters
* **Definition:** Optional filters added to the end of a URL starting with a `?`. *(Query parameters URL ke end me query sign (?) ke baad lagaye jaane wale search/filter inputs hain).*
* **Example:**
  ```text
  https://api.vjti.edu/students?branch=CS&feesPaid=false
  ```
* **Key Points:**
  * Used inside GET requests.
  * Chained using `&`.
* **Common Mistakes:** Sending passwords or personal secrets in query parameters where they are visible in logs.

---

### 7. Request Body
* **Definition:** The raw data packet sent to the server to create or edit resources. *(Request Body wo main content packet hai jo POST/PUT/PATCH requests me server par bheja jata hai).*
* **Example:**
  ```json
  { "studentName": "Amit Patel", "branch": "IT" }
  ```
* **Key Points:**
  * Primarily used by POST, PUT, and PATCH.
  * Usually serialized as a JSON string.
* **Common Mistakes:** Passing raw JavaScript objects directly without converting them to a JSON string.

---

### 8. Response Validation
* **Definition:** Verifying response elements against expected results. *(Server dwara bheje gaye code, headers, aur body ko assert karna).*
* **Example:**
  ```javascript
  expect(response.status()).toBe(200);
  ```
* **Key Points:**
  * Always check status code first before parsing the response body.
  * Assert content-type headers to ensure correct formatting.
* **Common Mistakes:** Parsing the body before validating the status, causing tests to crash on server failures.

---

### 9. GET
* **Definition:** The HTTP method used to read data. *(GET method ka use database se data read karne ke liye kiya jata hai).*
* **Example:**
  ```javascript
  const response = await request.get('/users/1');
  expect(response.status()).toBe(200);
  ```
* **Key Points:**
  * Safe and idempotent.
  * Does not have a request body payload.
* **Common Mistakes:** Expecting GET requests to update database values.

---

### 10. POST
* **Definition:** The HTTP method used to create a new resource. *(POST method ka use server par naya data record banane ke liye kiya jata hai).*
* **Example:**
  ```javascript
  const response = await request.post('/users', { data: { name: 'Suresh' } });
  expect(response.status()).toBe(201);
  ```
* **Key Points:**
  * Unsafe and non-idempotent.
  * Usually returns a `201 Created` status code and an auto-assigned ID.
* **Common Mistakes:** Using `200 OK` assertions when the server is designed to return `201 Created`.

---

### 11. PUT
* **Definition:** The HTTP method used to completely replace an existing resource. *(PUT method ka use existing record ko naye values se completely replace karne ke liye kiya jata hai).*
* **Example:**
  ```javascript
  const response = await request.put('/users/1', { data: { name: 'Rahul R', branch: 'CS' } });
  expect(response.status()).toBe(200);
  ```
* **Key Points:**
  * Idempotent but unsafe.
  * Missing fields in the payload will be cleared or overwritten.
* **Common Mistakes:** Omitting optional fields during a PUT call, which can wipe out existing values on the server.

---

### 12. PATCH
* **Definition:** The HTTP method used to partially update a resource. *(PATCH method ka use database me kisi record ke specific fields ko partially update karne ke liye hota hai).*
* **Example:**
  ```javascript
  const response = await request.patch('/users/1', { data: { email: 'rahul.new@gmail.com' } });
  expect(response.status()).toBe(200);
  ```
* **Key Points:**
  * Unsafe method.
  * Saves bandwidth by transmitting only modified fields.
* **Common Mistakes:** Confusing PATCH with PUT and sending the complete profile object.

---

### 13. DELETE
* **Definition:** The HTTP method used to remove resources. *(DELETE method ka use server database se kisi record ko hatane ke liye kiya jata hai).*
* **Example:**
  ```javascript
  const response = await request.delete('/users/1');
  expect([200, 204]).toContain(response.status());
  ```
* **Key Points:**
  * Idempotent but unsafe.
  * Successful deletions can return `200 OK` or `204 No Content`.
* **Common Mistakes:** Expecting a response body payload when the status code is `204`.

---

## Assignment Summary

During this session, we practice the following automated API test implementations:
1. **GET Student List Validation:** Verifying status code `200`, checking JSON response array length, and validating object properties.
2. **POST Course Registration Validation:** Building a request payload, verifying status code `201 Created`, and asserting that the server returned an auto-assigned database ID.
3. **DELETE Student Record Validation:** Executing a delete operation and verifying the return status code is a valid success code (`200` or `204`).

---

## Quick Revision Sheet

| Operation | HTTP Verb | Success Status | Idempotent? | Request Body? |
| --- | --- | --- | --- | --- |
| **Create** | POST | `201 Created` | No | Yes |
| **Read** | GET | `200 OK` | Yes | No |
| **Replace**| PUT | `200 OK` | Yes | Yes |
| **Modify** | PATCH | `200 OK` | No (varies) | Yes |
| **Delete** | DELETE | `200 / 204` | Yes | No |

---

## Important Takeaways

1. **API Testing Value:** Runs significantly faster and detects backend bugs earlier than UI testing.
2. **REST URI Rules:** Focus on nouns (resources) instead of action verbs.
3. **Idempotency Rule:** Make sure you understand which operations are safe to run repeatedly (like GET, PUT) versus those that generate side effects (like POST).
4. **Header Configurations:** Always verify `Content-Type` is set correctly so the server can parse your payloads.
5. **Playwright request Fixture:** Playwright provides a direct `request` fixture that makes launching HTTP calls fast and easy without requiring external packages.
