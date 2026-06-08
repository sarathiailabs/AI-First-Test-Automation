# Day 12: Advanced API Testing – Assignments

This assignment file contains theoretical questions and practical programming tasks designed to reinforce advanced API testing concepts, including OAuth tokens, JSON extraction, API chaining, and environment configurations.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is a JSON Web Token (JWT)? Explain the difference between its Header, Payload, and Signature. Can a user modify the payload contents client-side?

### Question 2 🔥 **Frequently Asked**
What is the difference between Authentication (AuthN) and Authorization (AuthZ)? Give a real-world scenario from a banking application context.

### Question 3 📌 **Important**
What is **API Chaining**? Describe a scenario where an automation script has to execute API chaining to complete a test path.

### Question 4 📌 **Important**
Why are Environment Variables used to store API endpoints and credentials? Why should you never commit `.env` files to git repositories?

### Question 5 💡 **Good to Know**
Explain how OAuth 2.0 uses "Access Tokens" to authorize third-party client applications without exposing user passwords.

---

## Practical Assignments

### Assignment 1: VJTI Login API Validation

* **Interview Relevance:** Automating login endpoints is the gateway to validating secure platforms. Tests ability to handle POST parameters, read auth responses, and verify success flags.
* **Difficulty Level:** Easy-Intermediate
* **Concepts Covered:** `request.post()`, JSON request payload, assertion of status code, login success check.

#### Problem Statement
Write a Playwright test named `api-auth-login.spec.ts` that triggers a login POST call to an authentication endpoint, validates the response status code is `200 OK`, and asserts that a success indicator is returned.

**Login Mock Endpoint:**
`POST https://reqres.in/api/login`

**Request Body (JSON):**
```json
{
  "email": "eve.holt@reqres.in",
  "password": "cityslicka"
}
```

#### Requirements
1. Send a POST request to `https://reqres.in/api/login` with the email and password payload.
2. Assert that the status code is `200 OK`.
3. Parse the JSON response body.
4. Assert that the response contains a property named `token` and that its value is defined.

#### Expected Output
```text
  ✓ POST VJTI Student Login API Validation (Pass)
```

#### Hints
* Pass credentials directly in the `data` block of the request options.

---

### Assignment 2: JWT Token Extraction and Secure Request Injection

* **Interview Relevance:** Passing dynamically extracted tokens to secure requests is a mandatory QA skill. Tests ability to retrieve a token and inject it into authorization headers.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Token extraction, `Authorization` header injection, template literals.

#### Problem Statement
Write a Playwright test named `api-token-extraction.spec.ts` that logs into an API endpoint, extracts the generated access token, and uses that token to validate access to a secure user directory endpoint.

#### Requirements
1. Send a POST request to `https://reqres.in/api/login` with credentials:
   - Email: `eve.holt@reqres.in`
   - Password: `cityslicka`
2. Extract the `token` string from the JSON response body.
3. Send a GET request to a mock secure endpoint: `https://jsonplaceholder.typicode.com/posts/1`
4. Inject the extracted token into the GET request's headers using the format:
   ```javascript
   headers: {
     'Authorization': `Bearer ${extractedToken}`
   }
   ```
5. Assert that the GET request status code is `200 OK`.

#### Expected Output
```text
  ✓ Token Extraction and Secure Injection Validation (Pass)
```

#### Hints
* Store the token in a local variable before passing it to the next request headers.

---

### Assignment 3: E2E CRUD Chaining with Environment variables

* **Interview Relevance:** End-to-end chaining using environment variables represents production framework testing. Tests capability to run full lifecycles securely using external configs.
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** API chaining, CRUD operations, `.env` file configuration, `process.env`.

#### Problem Statement
Write an end-to-end Playwright test named `api-crud-chaining.spec.ts` that configures environment variables, creates a post resource (POST), verifies its creation (GET), updates it (PATCH), and deletes it (DELETE).

#### Requirements
1. Create a local `.env` file in your project folder with:
   ```text
   TEST_API_URL=https://jsonplaceholder.typicode.com
   ```
2. Read the URL dynamically inside the test using `process.env.TEST_API_URL`.
3. **CREATE (POST):** Create a new post to `/posts` with payload `{ title: 'Advanced Chaining', body: 'CRUD lifecycle', userId: 1 }`. Verify status code is `201`.
4. **READ (GET):** Read user details from `/posts/1` (using ID 1 as mock target). Verify status code is `200`.
5. **UPDATE (PATCH):** Update post 1 title to `'Updated Title Chaining'`. Verify status code is `200`.
6. **DELETE (DELETE):** Delete post 1. Verify status code is either `200` or `204`.

#### Expected Output
```text
  ✓ E2E CRUD Chaining with Environment variables (Pass)
```

#### Hints
* Load env parameters using `process.env.VARIABLE_NAME`.
