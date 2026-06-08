# Day 12: Advanced API Testing – Solutions

This file contains solutions for the theory questions and practical programming assignments assigned for Day 12.

---

## Theory Question Solutions

### Question 1
What is a JSON Web Token (JWT)? Explain the difference between its Header, Payload, and Signature. Can a user modify the payload contents client-side?

### Answer
* **JWT:** A secure, compact JSON token format used to transmit authenticated claims between a client and a server.
* **Structure:**
  * **Header:** Specifies the token type (JWT) and hash algorithm used (e.g. HS256).
  * **Payload:** Contains the actual data claims (e.g. `rollNo`, `name`, expiration time).
  * **Signature:** Formed by encoding header and payload, hashing them with a secret server-side key, and signing the hash.
* **Client-side Modification:** A user can decode and modify the payload text (since it's only Base64 encoded), but when the server receives it, the cryptographic signature check will fail because the payload hash no longer matches the signature. The server will reject the token immediately.

---

### Question 2
What is the difference between Authentication (AuthN) and Authorization (AuthZ)? Give a real-world scenario from a banking application context.

### Answer
* **Authentication (AuthN):** The step of verifying *who you are*.
  * *Bank Example:* Entering your customer ID and password to log into the NetBanking dashboard.
* **Authorization (AuthZ):** The step of verifying *what you are allowed to do*.
  * *Bank Example:* Once logged in, attempting to transfer money out of account A. The system checks if account A belongs to you (authorized) or someone else (unauthorized, resulting in a 403 Forbidden error).

---

### Question 3
What is **API Chaining**? Describe a scenario where an automation script has to execute API chaining to complete a test path.

### Answer
* **API Chaining:** Executing sequential API requests where parameter values returned in previous responses are extracted and used dynamically as arguments inside subsequent requests.
* **Scenario:** An e-commerce test path:
  1. `POST /login` $\rightarrow$ Extract the security token.
  2. `POST /cart` $\rightarrow$ Add an item, extract the `cartId` from the response.
  3. `POST /checkout` $\rightarrow$ Submit the checkout order passing both the security token in the header and the `cartId` in the request body.

---

### Question 4
Why are Environment Variables used to store API endpoints and credentials? Why should you never commit `.env` files to git repositories?

### Answer
* **Why used:** It decouples environment configurations (like base URLs) and secret keys from code scripts. This allows running tests against Staging or Prod environments without modifying code, and secures passwords.
* **Why not committed:** `.env` files contain plain-text database logins, API passwords, and security keys. If committed, anyone with repository access (or the public, if open-source) can view these credentials and breach the database.

---

### Question 5
Explain how OAuth 2.0 uses "Access Tokens" to authorize third-party client applications without exposing user passwords.

### Answer
OAuth 2.0 introduces a delegation layer. Instead of sharing your master password with a third-party app (e.g., giving a book-catalog app your Google password), you log into Google directly. Google prompts you to grant limited permissions. If approved, Google issues a temporary, scoped **Access Token** to the catalog app. The app shows this token to Google's databases to fetch only your name, keeping your main login password hidden.

---

## Programming Assignment Solutions

### Assignment 1: VJTI Login API Validation

#### Solution Code
```javascript
import { test, expect } from '@playwright/test';

test('POST VJTI Student Login API Validation', async ({ request }) => {
  // 1. Send POST request to login endpoint
  const response = await request.post('https://reqres.in/api/login', {
    data: {
      email: "eve.holt@reqres.in",
      password: "cityslicka"
    }
  });

  // 2. Validate response status code is 200 OK
  expect(response.status()).toBe(200);

  // 3. Parse JSON response body
  const body = await response.json();

  // 4. Validate that a token is returned
  expect(body.token).toBeDefined();
  expect(body.token.length).toBeGreaterThan(0);
});
```

#### Explanation
* **`request.post()`:** Sends credentials to the mock authentication server.
* **`expect(body.token).toBeDefined()`:** Asserts that the authentication transaction succeeded and the server returned a valid token property.

#### Output
```text
  ✓ POST VJTI Student Login API Validation (Pass - 180ms)
```

---

### Assignment 2: JWT Token Extraction and Secure Request Injection

#### Solution Code
```javascript
import { test, expect } from '@playwright/test';

test('Token Extraction and Secure Injection Validation', async ({ request }) => {
  // Step 1: Login to extract token
  const loginResponse = await request.post('https://reqres.in/api/login', {
    data: {
      email: "eve.holt@reqres.in",
      password: "cityslicka"
    }
  });
  expect(loginResponse.status()).toBe(200);

  const loginBody = await loginResponse.json();
  const extractedToken = loginBody.token; // Capture token value
  expect(extractedToken).toBeDefined();

  // Step 2: Inject token into headers of next request
  const secureResponse = await request.get('https://jsonplaceholder.typicode.com/posts/1', {
    headers: {
      'Authorization': `Bearer ${extractedToken}`
    }
  });

  // Assert secure API responds successfully
  expect(secureResponse.status()).toBe(200);
});
```

#### Explanation
* **`loginBody.token`:** Extracts the token from the first POST API call.
* **`headers` block:** Injects the Bearer authorization string dynamically into the subsequent GET call, simulating secure endpoint integration.

#### Output
```text
  ✓ Token Extraction and Secure Injection Validation (Pass - 220ms)
```

---

### Assignment 3: E2E CRUD Chaining with Environment Variables

#### Solution Code
```javascript
import { test, expect } from '@playwright/test';
import * as dotenv from 'dotenv';
import * as path from 'path';

// Load variables from .env file
dotenv.config({ path: path.resolve(__dirname, '../../.env') });

test('E2E CRUD Chaining with Environment variables', async ({ request }) => {
  // Retrieve target host from environment configuration
  const apiBaseUrl = process.env.TEST_API_URL || 'https://jsonplaceholder.typicode.com';

  // 1. CREATE (POST)
  const createResponse = await request.post(`${apiBaseUrl}/posts`, {
    data: {
      title: 'Advanced Chaining',
      body: 'CRUD lifecycle',
      userId: 1
    }
  });
  expect(createResponse.status()).toBe(201);
  const createdPost = await createResponse.json();
  expect(createdPost.id).toBeDefined();

  // 2. READ (GET) - Target specific entity
  const readResponse = await request.get(`${apiBaseUrl}/posts/1`);
  expect(readResponse.status()).toBe(200);
  const readPost = await readResponse.json();
  expect(readPost.id).toBe(1);

  // 3. UPDATE (PATCH) - Modify field
  const updateResponse = await request.patch(`${apiBaseUrl}/posts/1`, {
    data: {
      title: 'Updated Title Chaining'
    }
  });
  expect(updateResponse.status()).toBe(200);
  const updatedPost = await updateResponse.json();
  expect(updatedPost.title).toBe('Updated Title Chaining');

  // 4. DELETE (DELETE) - Clean up resource
  const deleteResponse = await request.delete(`${apiBaseUrl}/posts/1`);
  expect([200, 204]).toContain(deleteResponse.status());
});
```

#### Explanation
* **`dotenv.config()`:** Loads keys from `.env`.
* **`process.env.TEST_API_URL`:** Pulls URL configurations dynamically without hardcoding addresses.
* **E2E CRUD Cycle:** Chain is established by querying records sequentially, verifying creation, modification, and teardown.

#### Output
```text
  ✓ E2E CRUD Chaining with Environment variables (Pass - 340ms)
```
