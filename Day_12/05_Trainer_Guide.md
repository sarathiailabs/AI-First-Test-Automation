# Day 12: Advanced API Testing – Trainer Guide

This handbook helps instructors deliver Day 12: Advanced API Testing. Follow the lesson sequences, whiteboard outlines, and interactive student engagement benchmarks below.

---

## Session Opening

### Welcome Script
> *"Hello, everyone! Welcome to Day 12. Yesterday we completed API Testing Fundamentals. Today, we are taking a major step forward. We will learn how APIs secure themselves, how to handle tokens, how to chain multiple API requests together, and how to protect configurations using environment variables. Let's get started!"*

### Session Goal
By the end of this session, trainers will guide students to write automated end-to-end API integration tests. They will handle JWT tokens, manage login authorizations dynamically, chain sequential requests, and configure credentials securely using Dotenv.

### Motivation
> *"In the real world, you can't just call a secure bank API without a login token. If you write 100 tests, you don't want to run a slow UI login flow 100 times. You need to log in once via API, capture the token, and reuse it. Mastering Token Management and API Chaining makes your test suites robust, clean, and professional."*

---

## Topic 1: Authentication

### Trainer Introduction
> *"Authentication (AuthN) is like verifying your identity at the college entrance. You show your ID card (username/password). Authorization (AuthZ) is checking if you have rights to enter a specific room, like the principal's cabin. In APIs, we pass these identity proofs inside the headers, often using 'Bearer Tokens'."*

### Student Engagement Questions
1. *"What is the difference between showing your ID card (AuthN) and trying to access the college server room (AuthZ)?"*
2. *"How do we pass authorization parameters securely in an HTTP call?"*

### Whiteboard Teaching
```text
  [ CLIENT ] ──► Header: { Authorization: Bearer <key> } ──► [ SERVER ]
  (Identity checks out, permissions verify role scope)
```

### Teaching Flow
1. Define AuthN vs AuthZ.
2. Outline Basic Auth, API keys, and Bearer Tokens.
3. Live code a Basic Authentication assertion block.

### Topic Recap
Authentication verifies credentials, and Authorization restricts resource access.

### Transition Script
> *"Bearer tokens are common. A popular format for these tokens is JWT. Let's understand what is inside a JSON Web Token."*

---

## Topic 2: JWT (JSON Web Tokens)

### Trainer Introduction
> *"A JWT is like a university exam hall ticket. It contains your name, roll number, and department. It is signed by the Controller of Examinations. Even if you try to change your name on the card, the invigilator knows it's tampered with because the signature won't match. JWT payloads are Base64 encoded JSON objects, secured by cryptographic signatures."*

### Student Engagement Questions
1. *"Can you decode a JWT payload easily if you intercept it?"*
2. *"Is a JWT encrypted? (Answer: No, it is only encoded. Never put passwords inside it)."*

### Whiteboard Teaching
Draw the dot-separated JWT parts:
```text
  Header (Algorithm HS256) . Payload (User Claims) . Signature (Hash Verification)
  [ xxxxx ]                . [ yyyyy ]             . [ zzzzz ]
```

### Live Coding Demonstration
#### Step 1: Type
Open console:
```javascript
const payload = "eyJyb2xsTm8iOjEwMDEsIm5hbWUiOiJSYWh1bCJ9"; // Mock payload
const decoded = Buffer.from(payload, 'base64').toString('utf-8');
console.log(decoded);
```
#### Step 3: Expected Output
```json
{"rollNo":1001,"name":"Rahul"}
```

### Topic Recap
JWT packages user claims in a compact, signature-verified format.

### Transition Script
> *"JWTs are used for authentication. But what if we want to authorize third-party apps, like using Google to log into a bus booking website? We use OAuth."*

---

## Topic 3: OAuth Concepts

### Trainer Introduction
> *"OAuth is like using a valet key for parking. When you hand your car to a valet driver, you don't give them your master house keys. You give them a valet key that only drives the car for a limited distance. In software, OAuth issues an 'Access Token' to third-party apps, letting them fetch specific details (like name) without giving them your master Google password."*

### Student Engagement Questions
1. *"When you log into an app using 'Sign in with Google', does the app get your Google password? Why is this safe?"*

### Whiteboard Teaching
Draw the OAuth flow:
```text
  [ User ] ──► Grants permissions ──► [ Auth Server (Google) ]
                                                │
  [ App ]  ◄── Access Token (Valet Key) ◄───────┘
```

### Topic Recap
OAuth provides delegated access using scoped tokens instead of exposing master passwords.

### Transition Script
> *"Whether we use JWT or OAuth, we get a token from the server. How do we extract and inject this token across our automation scripts? Let's check Token Management."*

---

## Topic 4: Token Management

### Trainer Introduction
> *"Token Management is the design pattern of calling a login API once, extracting the token from the response body, and automatically appending it to subsequent secure API headers."*

### Teaching Flow
1. Write a `beforeAll` hook to automate logging in.
2. Store the token in a global variable.
3. Inject the variable inside subsequent request headers.

### Live Coding Demonstration
#### Step 1: Type
Show code structure:
```javascript
import { test, expect } from '@playwright/test';

let globalToken = "";

test.beforeAll(async ({ request }) => {
  const login = await request.post('https://reqres.in/api/login', {
    data: { email: "eve.holt@reqres.in", password: "cityslicka" }
  });
  const body = await login.json();
  globalToken = body.token;
});
```

### Topic Recap
Token Management logins once and stores auth states to authorize downstream tests.

### Transition Script
> *"With token management in place, we can feed the output of one API as input to another. This is called API Chaining."*

---

## Topic 5: API Chaining

### Trainer Introduction
> *"API Chaining is like booking a train ticket. First, you search for the train (API 1) and extract the train ID. Next, you select a seat (API 2) and extract the seat number. Finally, you pay (API 3) using both the train ID and seat number. One API's output becomes the next API's input."*

### Student Engagement Questions
1. *"Can you book a seat if the search API fails to return a Train ID? Why is integration testing important here?"*

### Whiteboard Teaching
```text
  [ GET /users ] ──► Extract userID: 1 ──► [ PUT /users/1 ] (Update profile)
```

### Topic Recap
API Chaining sequences dependent calls together using dynamic outputs.

### Transition Script
> *"To chain APIs, we must know how to pull specific variables out of a JSON response. Let's learn about Data Extraction."*

---

## Topic 6: Data Extraction

### Trainer Introduction
> *"Data Extraction is using JavaScript dot notation to locate and extract nested keys from a parsed JSON body. For example, if a company has address details, we read: `body.company.address.city`."*

### Live Coding Demonstration
#### Step 1: Type
```javascript
const responseBody = {
  student: {
    details: { rollNo: 1001, courses: ["CS", "IT"] }
  }
};
// Extract CS course
const firstCourse = responseBody.student.details.courses[0];
console.log(firstCourse);
```
#### Step 3: Expected Output
```text
CS
```

### Topic Recap
Data extraction drills down into response JSON trees to pull out targeted fields.

### Transition Script
> *"We can now chain requests and extract data. Let's combine these steps to validate a resource lifecycle using CRUD Validation."*

---

## Topic 7: CRUD Validation

### Trainer Introduction
> *"CRUD Validation is validating the full lifecycle of an entity. We POST (Create) a student, GET (Read) to check if it exists, PATCH (Update) to edit their email, and DELETE (Delete) to clean up."*

### Whiteboard Teaching
Draw the E2E lifecycle loop:
```text
  Create (POST) ──► Read (GET) ──► Update (PATCH) ──► Delete (DELETE)
```

### Topic Recap
CRUD validation checks entity lifecycles to ensure database state alignment.

### Transition Script
> *"To run CRUD tests, we need environment configurations. Let's study Environment Variables."*

---

## Topic 8: Environment Variables

### Trainer Introduction
> *"Environment variables store host targets and authentication secrets outside the codebase (usually in `.env` files). This prevents hardcoding passwords in Git and lets us switch targets from Staging to Production easily."*

### Student Engagement Questions
1. *"Why should you never commit database passwords to a public GitHub repository?"*

### Live Coding Demonstration
#### Step 1: Type
```javascript
import { test, expect } from '@playwright/test';
import * as dotenv from 'dotenv';
dotenv.config();

test('Env check', async () => {
  const host = process.env.TEST_API_URL || 'https://reqres.in';
  console.log(`Hitting host: ${host}`);
});
```

### Topic Recap
Environment variables isolate configurations and secrets externally.

---

## Session Closing

### Session Summary
* We learned Authentication (identity verification) vs Authorization (permissions).
* We examined JWT token structures and OAuth delegated access models.
* We automated Token Management, API Chaining, and JSON data extraction.
* We executed complete CRUD lifecycles using environment configurations.

### Knowledge Check Questions
1. *"Is a JWT encrypted? (Answer: No, only encoded)."*
2. *"Which status code verifies success for resource deletion? (Answer: 200/204)."*
3. *"Why do we use the Bearer header prefix?"*
4. *"Where do we fetch variables loaded from a `.env` file in NodeJS? (Answer: process.env)."*
5. *"Why is API Chaining required in cart checkout automation?"*

### Homework Guidance
Instruct students to complete assignments inside the `Day_12` directory:
1. `api-auth-login.spec.ts`
2. `api-token-extraction.spec.ts`
3. `api-crud-chaining.spec.ts`

### Next Session Preview
In Day 13, we transition into Automation Framework Development, studying design patterns like the Page Object Model (POM) and modular framework configurations.
