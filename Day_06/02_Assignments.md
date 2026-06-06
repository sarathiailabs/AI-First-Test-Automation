# Day 6: Web Fundamentals for Testers – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of Client-Server Architecture, browser storages, HTTP status codes, DOM elements, and API authentication models. Please attempt all questions.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the difference between **Cookies**, **Local Storage**, and **Session Storage** in terms of lifetime, capacity, and whether they are automatically sent to the server?

### Question 2 🔥 **Frequently Asked**
What is the difference between a **401 Unauthorized** and a **403 Forbidden** status code? Provide a concrete scenario in automated QA testing where you would expect to assert each code.

### Question 3 📌 **Important**
What is the difference between **Server-Side Rendering (SSR)** and **Client-Side Rendering (CSR)**? In automated UI testing (e.g., using Playwright), how does CSR affect element waits and visibility checks?

### Question 4 📌 **Important**
What does the **HttpOnly** attribute on a cookie accomplish? Why is it considered a critical security practice for session cookies, and can automated scripts read it using `document.cookie`?

### Question 5 💡 **Good to Know**
Explain the structure of a **JWT Token**. Why is JWT preferred for REST APIs over traditional session-cookie models in distributed microservice architectures?

---

## Practical Assignments

### Assignment 1: Analyze Login Request (Network Log Parsing)

* **Interview Relevance:** Tests your ability to inspect raw network traffic, analyze headers, payloads, and response metadata — a foundational skill for API automation and bug triage.
* **Difficulty Level:** Beginner-Intermediate
* **Concepts Covered:** HTTP Request Method, Headers, Payload, Response Codes, Cookies.

#### Problem Statement
Below is a raw dump of an HTTP login request captured from the network tab while a student attempted to log into the VJTI Fees portal. Analyze the payload and answer the inspection questions.

**Captured HTTP Request Log:**
```text
POST /api/v1/auth/login HTTP/1.1
Host: erp.vjti.ac.in
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/124.0.0.0
Content-Type: application/json
Accept: application/json
Origin: https://erp.vjti.ac.in
Referer: https://erp.vjti.ac.in/portal/login

{
  "studentRollNo": 1005,
  "portalPassword": "SecureStudentPass99!"
}
```

#### Requirements
Parse the raw log above and answer:
1. What is the **HTTP Method** used, and what is the **Target URL path**?
2. What header indicates that the request body is sent in JSON format?
3. List the parameters and their values sent inside the request payload body.
4. If the server validates these credentials successfully:
   * What **HTTP Status Code** should be returned?
   * If the server issues a session identifier to the browser, which response header is used to set the cookie?

---

### Assignment 2: Inspect Browser Storage (ERP Theme & Sessions)

* **Interview Relevance:** Verifies your understanding of how web application state is managed locally in browser contexts, which is critical for configuring browser contexts and test state isolation.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Cookies, Local Storage, Session Storage, Tab Lifecycles.

#### Problem Statement
A student logs into the VJTI Hostel Allocation portal. The application performs the following browser storage operations:
1. Installs a cookie: `session_id=vjti-hostel-xyz999; HttpOnly; Secure; SameSite=Strict`.
2. Stores a value in Local Storage: `themeMode="dark"`.
3. Stores a value in Session Storage: `lastSearchedRoom=105`.

#### Requirements
Answer the following behavioral questions:
1. Explain how the browser automatically handles the `session_id` cookie on subsequent HTTP requests to the hostel portal.
2. If a malicious script attempts to run `console.log(document.cookie)`, will the `session_id` value print in the console? Explain why.
3. Contrast the lifespans of the stored items:
   * What happens to the `themeMode="dark"` value when the student closes the browser tab and restarts the computer next week?
   * What happens to the `lastSearchedRoom=105` value when the student closes the active portal tab?

---

### Assignment 3: JWT Token Authorization Flow

* **Interview Relevance:** JWT is the industry standard for API authorization; QAs must be confident in debugging JWT flows and passing authentication headers in automation scripts.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** JSON Web Tokens (JWT), Authorization Headers, Bearer Tokens, API Workflows.

#### Problem Statement
Analyze the authorization workflow for a student downloading their semester fee receipt. Map out the sequence of token exchanges and header parameters.

```text
  [ Student Browser ] ─── 1. POST Credentials ───► [ Auth Server ]
  [ Student Browser ] ◄─── 2. Returns JWT Token ── [ Auth Server ]
          │
  (Stores Token in Session Storage)
          │
  [ Student Browser ] ─── 3. GET Receipt + Header ─► [ Receipt API ]
  [ Student Browser ] ◄─── 4. Returns PDF file ─── [ Receipt API ]
```

#### Requirements
1. Describe what three parts make up the JWT token returned in Step 2.
2. In Step 3, the browser must send the token back to the Receipt API to authorize the file download. Write the exact **HTTP Authorization Header** format used to transmit this token (assume the token is `eyJ0b2tlbiI6InZqdGkifQ`).
3. If a student attempts to access the receipt with a tampered token (e.g. changing the payload contents manually), what HTTP Status Code should the Receipt API return? Explain why.
