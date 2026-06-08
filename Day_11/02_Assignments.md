# Day 11: API Testing Fundamentals – Assignments

This assignment file contains theoretical questions and practical programming tasks designed to reinforce API testing concepts. You will practice writing assertions for GET, POST, and DELETE requests using Playwright.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the difference between `PUT` and `PATCH` HTTP methods? Give a real-world scenario where you would use `PATCH` instead of `PUT`.

### Question 2 🔥 **Frequently Asked**
Explain the concept of **Idempotency** in HTTP methods. Which HTTP methods are idempotent, and why is `POST` considered non-idempotent?

### Question 3 📌 **Important**
What is the difference between status codes `401 Unauthorized` and `403 Forbidden`? Give a concrete example of both in the context of a student portal.

### Question 4 📌 **Important**
Why do we use the `Content-Type` header in HTTP requests? What happens if you send a JSON body payload in a `POST` request but set the `Content-Type` header to `text/plain`?

### Question 5 💡 **Good to Know**
When writing API automation tests, why is it considered a best practice to validate the response headers and response status before validating the response body data?

---

## Practical Assignments

### Assignment 1: VJTI Student List GET API Validation

* **Interview Relevance:** Validating retrieval APIs (GET) is the most basic yet frequent task in API automation. Tests your ability to handle response structures, status codes, content-type headers, and array length assertions.
* **Difficulty Level:** Easy-Intermediate
* **Concepts Covered:** `request.get()`, Status assertion, Header validation, JSON body array length, object property checking.

#### Problem Statement
Write a Playwright test named `api-get-students.spec.ts` that sends a GET request to a student list API, validates the status code, content-type header, and verifies that the return list contains at least one active student.

**Mock Endpoint API Response:**
`GET https://jsonplaceholder.typicode.com/users` (representing VJTI students list)
Response format: Array of user objects.

#### Requirements
1. Send a `GET` request to `https://jsonplaceholder.typicode.com/users` using Playwright's `request` fixture.
2. Assert that the response status code is exactly `200`.
3. Assert that the response header `'content-type'` contains `'application/json'`.
4. Parse the response body as a JSON array.
5. Assert that the array is not empty (length $> 0$).
6. Access the first student object and assert that the `id` is `1` and the `name` is a non-empty string.

#### Expected Output
```text
  ✓ GET VJTI Student List API Validation (Pass)
```

#### Hints
* Access headers using `response.headers()`.
* Access status using `response.status()`.
* Parse body using `await response.json()`.

---

### Assignment 2: VJTI Course Registration POST API Validation

* **Interview Relevance:** Creating resources via POST requests is a core business operation. Test validations evaluate ability to construct and send headers, build JSON payloads, and assert correct response code mapping (like `201 Created`).
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `request.post()`, Payload creation, `Content-Type` definition, status code checking, ID presence assertion.

#### Problem Statement
Write a Playwright test named `api-post-registration.spec.ts` that automates registerting a student for a scholarship. Send a JSON body payload, validate the status code is `201 Created`, and verify that the response returns the registered data along with an auto-assigned ID.

**Target Endpoint:**
`POST https://jsonplaceholder.typicode.com/posts` (representing VJTI registration path)

**Request Body (JSON):**
```json
{
  "studentName": "Aditya Verma",
  "rollNo": 1085,
  "courseCode": "CS-302",
  "appliedDate": "2026-06-08"
}
```

#### Requirements
1. Send a `POST` request to `https://jsonplaceholder.typicode.com/posts` using Playwright's `request` context.
2. Pass the JSON request body matching the payload structure shown above.
3. Assert that the response status code is `201` (Created).
4. Parse the response body.
5. Assert that the response body contains the property `id` (demonstrating the database created a new ID).
6. Assert that the returned `studentName` matches the value sent (`"Aditya Verma"`).

#### Expected Output
```text
  ✓ POST VJTI Course Registration API Validation (Pass)
```

#### Hints
* Pass payloads inside the `data` option of the request object:
  ```javascript
  await request.post('URL', {
    data: { ... }
  });
  ```

---

### Assignment 3: Student Record deletion (DELETE API Validation)

* **Interview Relevance:** Testing DELETE operations ensures database hygiene. Teaches validation of success states (like `200` or `204`) and confirms idempotency logic.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `request.delete()`, status assertions (`200 OK` or `204 No Content` validations).

#### Problem Statement
Write a Playwright test named `api-delete-student.spec.ts` that automates deleting a student record. Trigger a DELETE request, validate that the operation was successful, and handle variable success codes.

**Target Endpoint:**
`DELETE https://jsonplaceholder.typicode.com/posts/1` (representing VJTI student record 1)

#### Requirements
1. Send a `DELETE` request to `https://jsonplaceholder.typicode.com/posts/1` using the `request` context.
2. Assert that the status code returned by the server is either `200` (OK) or `204` (No Content).
3. Validate that the response contains no data if the status is `204`.

#### Expected Output
```text
  ✓ DELETE Student Record API Validation (Pass)
```

#### Hints
* Use `expect([200, 204]).toContain(response.status())` to allow either status code to pass.
