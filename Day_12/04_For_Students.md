# Day 12: Advanced API Testing – Student Revision Notes

This revision sheet provides a quick-revision summary of definitions, examples, and key points for all topics covered in Day 12.

---

## Topic Revision

### 1. Authentication
* **Definition:** Verifying who a user is (AuthN) versus checking what they are allowed to do (AuthZ). *(Authentication identity verification hai aur Authorization permission validation hai).*
* **Example Header:**
  ```text
  Authorization: Basic cG9zdG1hbjpwYXNzd29yZA==
  ```
* **Key Points:**
  * Authentication uses credentials, keys, or Bearer tokens.
  * Authorization determines access privileges based on user roles.
* **Common Mistakes:** Confusing authentication with authorization inside test reports.

---

### 2. JWT (JSON Web Tokens)
* **Definition:** A secure token structure containing encoded data claims signed cryptographically. *(JWT ek encrypted data card hai jise tamper nahi kiya ja sakta).*
* **Example Structure:**
  `header.payload.signature`
* **Key Points:**
  * Base64 encoded but not encrypted.
  * Secret keys verify signatures on the server to prevent data modifications.
* **Common Mistakes:** Putting plain-text passwords or secret database values inside JWT payloads.

---

### 3. OAuth Concepts
* **Definition:** An authorization standard allowing secure, delegated application access without sharing user passwords. *(OAuth doosre systems se secure access token leta hai login credentials expose kiye bina).*
* **Key Points:**
  * Uses Access Tokens instead of passwords.
  * Involves Resource Owner, Client, Authorization Server, and Resource Server.

---

### 4. Token Management
* **Definition:** Fetching authentication tokens once at login and injecting them automatically into subsequent API calls. *(Token ko login call se nikal kar aage ke API headers me insert karna).*
* **Example Implementation:**
  ```javascript
  const token = loginBody.token;
  const secureResponse = await request.get('/secure-data', {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  ```
* **Key Points:**
  * Avoids redundant logins.
  * Tokens can be managed globally in test suites.
* **Common Mistakes:** Hardcoding expiration-prone token strings in automation frameworks.

---

### 5. API Chaining
* **Definition:** Sequentially executing requests where parameters returned in one response are fed as inputs to the next. *(Ek API response data ko doosre API request details me chain karna).*
* **Example:**
  ```javascript
  const postId = responseBody.id;
  await request.delete(`/posts/${postId}`);
  ```
* **Key Points:**
  * Tests multi-step workflows.
  * Requires precise response data capture.

---

### 6. Data Extraction
* **Definition:** Retrieving targeted properties or array indices from structured JSON response payloads. *(JSON response body se target value select karna).*
* **Example:**
  ```javascript
  const email = body.address.geo.lat;
  ```
* **Key Points:**
  * Always verify that parent objects exist.
  * Use optional chaining (`?.`) to avoid crashing tests on missing elements.
* **Common Mistakes:** Attempting to read nested keys on undefined structures, causing script crashes.

---

### 7. CRUD Validation
* **Definition:** Executing sequential Create, Read, Update, and Delete actions on a database entity to validate lifecycle consistency. *(Naya record create, read, edit aur delete karke lifecycle verify karna).*
* **Key Points:**
  * Validates database alignment.
  * Maintains test isolation by cleaning up databases after test execution.

---

### 8. Environment Variables
* **Definition:** Configuration parameters managed outside the codebase (often in `.env` files) to store sensitive keys and API targets. *(Sensitive login data aur base URLs ko code se alag environment configs me rakhna).*
* **Example Syntax:**
  `process.env.TEST_API_URL`
* **Key Points:**
  * Secures credentials.
  * Allows switching environment targets dynamically.
* **Common Mistakes:** Committing the `.env` file containing secrets to GitHub instead of ignoring it.

---

## Assignment Summary

During this session, we practice:
1. **VJTI Login API Validation:** Automated login credentials verification, check status, and capture tokens.
2. **JWT Token Extraction & Injection:** Capture dynamic credentials and inject them into secure GET headers.
3. **E2E CRUD Chaining using Env Configuration:** Read host directories dynamically from `.env` files and execute a complete POST $\rightarrow$ GET $\rightarrow$ PATCH $\rightarrow$ DELETE cycle.

---

## Quick Revision Sheet

| Concept | API Role | Code Syntax | Key Target |
| --- | --- | --- | --- |
| **Auth** | Identify client | `{ headers: { 'Authorization': ... } }` | Secure API access |
| **Extraction** | Retrieve keys | `const token = body.token` | Capture dynamic variables |
| **Chaining** | Workflow link | `request.put('/posts/' + extractedId)` | Test integration paths |
| **Env Var** | Secure secrets | `process.env.API_URL` | Decouple system configs |
| **CRUD** | Entity lifecycle | Sequential: POST $\rightarrow$ GET $\rightarrow$ PATCH $\rightarrow$ DELETE | Assert database consistency |

---

## Important Takeaways

1. **Security Isolation:** Keep endpoints and tokens isolated inside environment configurations to enforce security.
2. **Workflow Chaining:** Dynamic extraction is critical to simulate actual user integration paths (e.g. login, select, purchase).
3. **Decoupled Auth:** OAuth tokens act as valet keys, restricting access to third-party clients without sharing passwords.
