# Day 11: API Testing Fundamentals – Solutions

This file contains solutions for the theory questions and practical programming assignments assigned for Day 11.

---

## Theory Question Solutions

### Question 1
What is the difference between `PUT` and `PATCH` HTTP methods? Give a real-world scenario where you would use `PATCH` instead of `PUT`.

### Answer
* **PUT:** Replaces the *entire* target resource with the new request payload. If any field is omitted from the request body, the server will either overwrite it with a default value, set it to `null`, or fail the validation.
* **PATCH:** Applies *partial* updates to the resource. Only the fields sent in the request body are modified on the server; other fields remain unchanged.
* **Real-world Scenario:** If a student changes only their phone number on the college portal, we use **PATCH** to send `{ "phone": "9876543210" }`. Using **PUT** would require sending their entire profile (name, roll number, email, address, GPA, branch) just to update the phone number, wasting network bandwidth.

---

### Question 2
Explain the concept of **Idempotency** in HTTP methods. Which HTTP methods are idempotent, and why is `POST` considered non-idempotent?

### Answer
* **Idempotency:** An HTTP method is idempotent if making multiple identical requests to the server produces the exact same server state as making a single request. *(Ek request ko 1 baar run karo ya 100 baar, agar server database ki state bilkul change nahi hoti aur same rehti hai, toh use idempotent kehte hain).*
* **Idempotent Methods:** `GET`, `PUT`, `DELETE`.
* **Non-Idempotent Methods:** `POST`.
* **Why POST is Non-Idempotent:** Calling `POST` multiple times will create duplicate database records. For example, if you click the "Pay Now" button (POST request) 3 times due to a lagging UI, it could create 3 separate transactions and deduct money 3 times.

---

### Question 3
What is the difference between status codes `401 Unauthorized` and `403 Forbidden`? Give a concrete example of both in the context of a student portal.

### Answer
* **401 Unauthorized:** The client's identity is unknown to the server because authentication credentials are missing or invalid. *(Server ko nahi pata aap kaun hain - authentication fail).*
  * *Example:* Trying to access the VJTI student gradebook without logging in, or logging in with an expired session token.
* **403 Forbidden:** The client is authenticated (their identity is known), but they do not have the required access permissions to read or modify the resource. *(Server jaanta hai aap kaun hain, par aapko us data ko dekhne ki permission nahi hai - authorization fail).*
  * *Example:* A student logs in successfully and tries to access the teacher's grade modification API `/api/admin/change-marks`. The request is rejected with a 403 status code.

---

### Question 4
Why do we use the `Content-Type` header in HTTP requests? What happens if you send a JSON body payload in a `POST` request but set the `Content-Type` header to `text/plain`?

### Answer
* **Purpose:** The `Content-Type` header tells the server how to interpret and parse the incoming request body data.
* **What happens if set to text/plain:** If you send a JSON payload (e.g., `{"name": "Rahul"}`) but set the header to `text/plain`, the server treats the payload as a raw block of text instead of an object. The server's JSON parser will not process the fields, resulting in errors like `400 Bad Request` or null database records due to missing fields.

---

### Question 5
When writing API automation tests, why is it considered a best practice to validate the response headers and response status before validating the response body data?

### Answer
1. **Early Failure Detection:** If the server crashes and returns a `500 Internal Server Error` containing an HTML error page, trying to parse the response body as JSON (`await response.json()`) will crash your test execution immediately.
2. **Context Checking:** Checking the `Content-Type` header first ensures the response is actually JSON before you attempt to assert specific data keys inside the body, leading to cleaner test logs.

---

## Programming Assignment Solutions

### Assignment 1: VJTI Student List GET API Validation

#### Solution Code
```javascript
import { test, expect } from '@playwright/test';

test('GET VJTI Student List API Validation', async ({ request }) => {
  // 1. Send GET request to fetch user profiles
  const response = await request.get('https://jsonplaceholder.typicode.com/users');

  // 2. Validate response status code is 200 OK
  expect(response.status()).toBe(200);

  // 3. Validate response header content-type contains application/json
  const contentType = response.headers()['content-type'];
  expect(contentType).toContain('application/json');

  // 4. Parse response body as JSON array
  const studentList = await response.json();

  // 5. Assert that the array is not empty
  expect(studentList.length).toBeGreaterThan(0);

  // 6. Access the first student and validate fields
  const firstStudent = studentList[0];
  expect(firstStudent.id).toBe(1);
  expect(firstStudent.name).toBeDefined();
  expect(firstStudent.name.length).toBeGreaterThan(0);
});
```

#### Explanation
* **`request.get()`:** Hits the mock server endpoint to fetch data.
* **`response.status()` and `response.headers()`:** Extract status codes and header objects to run baseline assertions.
* **`toBeGreaterThan(0)`:** Confirms that student records were loaded from the database.
* **`firstStudent.name`:** Extracts the name property from the first array element and asserts it is defined and contains characters.

#### Output
```text
  ✓ GET VJTI Student List API Validation (Pass - 165ms)
```

---

### Assignment 2: VJTI Course Registration POST API Validation

#### Solution Code
```javascript
import { test, expect } from '@playwright/test';

test('POST VJTI Course Registration API Validation', async ({ request }) => {
  // Define student registration payload
  const registrationPayload = {
    studentName: "Aditya Verma",
    rollNo: 1085,
    courseCode: "CS-302",
    appliedDate: "2026-06-08"
  };

  // 1. Send POST request with JSON payload
  const response = await request.post('https://jsonplaceholder.typicode.com/posts', {
    data: registrationPayload
  });

  // 2. Assert status code is 201 Created
  expect(response.status()).toBe(201);

  // 3. Parse response body
  const responseBody = await response.json();

  // 4. Verify auto-generated resource ID is present in response
  expect(responseBody.id).toBeDefined();

  // 5. Verify returned studentName matches the payload sent
  expect(responseBody.studentName).toBe("Aditya Verma");
});
```

#### Explanation
* **`data: registrationPayload`:** Playwright automatically serializes the JavaScript object into a JSON string and configures the `Content-Type: application/json` header.
* **`toBe(201)`:** Asserts that the server successfully processed the POST request and created a new database entry.
* **`expect(responseBody.id).toBeDefined()`:** Verifies the backend successfully appended the record and returned a unique primary key ID.

#### Output
```text
  ✓ POST VJTI Course Registration API Validation (Pass - 192ms)
```

---

### Assignment 3: Student Record Deletion (DELETE API Validation)

#### Solution Code
```javascript
import { test, expect } from '@playwright/test';

test('DELETE Student Record API Validation', async ({ request }) => {
  // 1. Send DELETE request to target resource ID 1
  const response = await request.delete('https://jsonplaceholder.typicode.com/posts/1');

  // 2. Assert status code is either 200 OK or 204 No Content
  const statusCode = response.status();
  expect([200, 204]).toContain(statusCode);

  // 3. If response is 204 No Content, assert body is empty
  if (statusCode === 204) {
    const textBody = await response.text();
    expect(textBody).toBe("");
  }
});
```

#### Explanation
* **`request.delete('/posts/1')`:** Sends the delete request for resource ID `1`.
* **`toContain()`:** Checks if the returned status code matches one of the acceptable REST standards (`200` or `204`).
* **`response.text()`:** Reads the response as raw text. If the status is `204`, we assert that no body text was returned.

#### Output
```text
  ✓ DELETE Student Record API Validation (Pass - 122ms)
```
