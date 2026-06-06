# Day 6: Web Fundamentals for Testers – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and practical assignments from Day 6.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is the difference between **Cookies**, **Local Storage**, and **Session Storage** in terms of lifetime, capacity, and whether they are automatically sent to the server?

### Answer
Here is a direct comparison table commonly used to answer this in interviews:

| Feature | Cookies | Local Storage | Session Storage |
| :--- | :--- | :--- | :--- |
| **Lifetime (Life)** | Expired based on date/time set by server (or when session ends). | Persistent. Remains forever until manually deleted by code/user. | Temporary. Wiped instantly when the browser tab is closed. |
| **Capacity (Size)** | Very small (around 4 KB). | Large (around 5 MB to 10 MB). | Large (around 5 MB). |
| **Sent to Server?** | **Yes**, automatically sent in HTTP request headers on every call. | **No**, stays completely on the browser. | **No**, stays completely on the browser. |
| **Hinglish Helper** | *Cookies server ke paas har request pe apne aap jati hain, Local/Session Storage sirf browser ke memory me save rehti hain.* |

**Interview Tip:** Always highlight that **Cookies** are used for Authentication/Session tracking because they go to the server, while **Local/Session Storage** are used for UI state preferences (like Dark Mode theme or current form drafts).

---

### Question 2 🔥 **Frequently Asked**
What is the difference between a **401 Unauthorized** and a **403 Forbidden** status code? Provide a concrete scenario in automated QA testing where you would expect to assert each code.

### Answer
* **401 Unauthorized:** The user has not provided valid credentials (identity is not verified). *(User logged in nahi hai - identity proof missing hai).*
  * **Analogy:** Trying to enter a college building without showing your student ID card.
  * **QA Automation Scenario:** You make an API call to fetch `GET /api/v1/student/receipt` without passing the `Authorization` header. You assert the response status is `401`.
* **403 Forbidden:** The user is logged in, but does not have permission to access the requested resource. *(User logged in to hai, par uske paas us page ko dekhne ka permission nahi hai).*
  * **Analogy:** A student showing their valid ID card but trying to enter the Principal's private office or the Staff Server Room.
  * **QA Automation Scenario:** You log in as a standard "Student" (with a valid token) and try to trigger an admin-only endpoint like `DELETE /api/v1/admin/delete-student-record/1005`. You assert the response status is `403`.

---

### Question 3 📌 **Important**
What is the difference between **Server-Side Rendering (SSR)** and **Client-Side Rendering (CSR)**? In automated UI testing (e.g., using Playwright), how does CSR affect element waits and visibility checks?

### Answer
* **SSR (Server-Side Rendering):** The server generates the complete HTML page with data and sends it to the browser. The page is immediately viewable when it arrives.
* **CSR (Client-Side Rendering):** The server sends a nearly empty HTML page and a JavaScript file. The browser executes this JavaScript to dynamically build (render) the UI.
* **Hinglish Helper:** *SSR me server cooked food ready-made bhejta hai, CSR me raw materials aur recipe bhejta hai jo browser me cook hoti hai.*

#### Impact on Automated QA Testing (Playwright):
In CSR web apps, after navigating to a page (e.g., `page.goto()`), the elements are not immediately present or visible in the DOM because JavaScript is still fetching data and building the elements in the background.
* **Flakiness Risk:** If you attempt to click or read an element immediately, the test will crash with a "Target closed" or "Element not found" error.
* **Solution:** In Playwright, you must use dynamic waiting. Playwright's locator actions automatically wait for element visibility, but for CSR applications, we often need to explicitly wait for loading animations to disappear or elements to detach/attach:
  ```javascript
  // Explicitly wait for CSR dynamic content to load:
  await page.waitForSelector(".fees-table-row", { state: "visible" });
  ```

---

### Question 4 📌 **Important**
What does the **HttpOnly** attribute on a cookie accomplish? Why is it considered a critical security practice for session cookies, and can automated scripts read it using `document.cookie`?

### Answer
* **What it does:** The `HttpOnly` flag is a security setting applied by the server when setting a cookie. It prevents client-side JavaScript from accessing that cookie.
* **Why it is critical:** It protects the application from **Cross-Site Scripting (XSS)** attacks. If a hacker injects a malicious script into the website, they cannot read the session cookie to hijack the user's account.
* **Can automated scripts read it?**
  * **Browser JavaScript (`document.cookie`):** **No.** It will return an empty string or omit this cookie.
  * **Playwright/Selenium Automation:** **Yes.** Testing tools run at the browser-level/CDP (Chrome DevTools Protocol) level. Therefore, you can retrieve it using Playwright's native API:
    ```javascript
    const cookies = await context.cookies();
    // This will successfully list the HttpOnly cookie because it reads directly from browser storage, not document.cookie
    ```

---

### Question 5 💡 **Good to Know**
Explain the structure of a **JWT Token**. Why is JWT preferred for REST APIs over traditional session-cookie models in distributed microservice architectures?

### Answer
A JSON Web Token (JWT) is composed of 3 parts separated by dots (`.`):
1. **Header:** Contains the metadata about the token (Hashing algorithm, Type of token).
2. **Payload:** Contains the claims or actual user data (e.g. `rollNo: 1005`, `role: "student"`, `exp: 1717770000`).
3. **Signature:** Generated by hashing the encoded Header, encoded Payload, and a secret key known only to the server. This guarantees that the token has not been tampered with.
* **Format:** `Header.Payload.Signature`

#### Why JWT is preferred in Microservices:
* **Stateless Validation:** Traditional session cookies require the API server to query a database or cache (like Redis) on every request to verify the session.
* **No Database Lookup:** With JWT, any microservice (e.g., Receipt Service, Attendance Service) can verify the token's authenticity instantly by checking the **Signature** using the shared public/private key. No database call is needed, which makes microservices faster and highly scalable.

---

## Programming Assignment Solutions

### Assignment 1: Analyze Login Request (Network Log Parsing)

#### Solution Details
Let's parse the captured HTTP Request log from the VJTI Fees portal login attempt:
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

#### Answers to Requirements:
1. **HTTP Method & Target URL path:**
   * **HTTP Method:** `POST`
   * **Target URL Path:** `/api/v1/auth/login`
   * **Complete URL:** `https://erp.vjti.ac.in/api/v1/auth/login` (derived from the `Host` header and the request line)
2. **Header indicating JSON format:**
   * `Content-Type: application/json` (This tells the server that the request body payload contains JSON formatted data).
3. **Parameters and values inside the request payload body:**
   * `studentRollNo`: `1005` (Numeric)
   * `portalPassword`: `"SecureStudentPass99!"` (String)
4. **Successful Validation Behaviors:**
   * **HTTP Status Code:** `200 OK` (indicating login success and data returned) or `200` with the token payload.
   * **Response Cookie Header:** The server will send a `Set-Cookie` header in its HTTP response.
     * *Example:* `Set-Cookie: session_token=abc123xyz; Path=/; HttpOnly; Secure; SameSite=Strict`

#### Explanation
When a user inputs data and submits the login form on the client web page:
1. The browser wraps the inputs into a JSON body payload.
2. It sets the `Content-Type` header so the server understands how to parse the incoming text.
3. The server validates the credentials against its database.
4. If correct, the server generates a session ID and transmits it back inside the `Set-Cookie` response header. The browser automatically saves this cookie and attaches it to all subsequent requests to the host `erp.vjti.ac.in`.

---

### Assignment 2: Inspect Browser Storage (ERP Theme & Sessions)

#### Solution Details
Analysis of the storage components configured on the VJTI Hostel Allocation portal:
1. Cookie: `session_id=vjti-hostel-xyz999; HttpOnly; Secure; SameSite=Strict`
2. Local Storage: `themeMode="dark"`
3. Session Storage: `lastSearchedRoom=105`

#### Answers to Requirements:
1. **Automatic Cookie Handling:**
   * On every subsequent HTTP request to `erp.vjti.ac.in` (like clicking "View Rooms"), the browser automatically appends the `session_id=vjti-hostel-xyz999` cookie in the `Cookie` request header. The student does not need to attach it manually; this is handled natively by the browser's networking engine.
2. **Malicious Script Access (`document.cookie`):**
   * **No**, the `session_id` cookie will **not** print in the console.
   * **Reason:** The cookie has the `HttpOnly` flag enabled. This instructs the browser to block any access to this cookie from JavaScript APIs, successfully preventing XSS-based theft.
3. **Storage Lifespans Comparison:**
   * `themeMode="dark"` (Local Storage): **Persistent.** When the student closes the browser tab and returns next week, the value will still be there. Local storage does not expire unless cleared manually.
   * `lastSearchedRoom=105` (Session Storage): **Wiped.** The moment the student closes the active portal tab, the session context is destroyed, and the value is permanently removed from the browser.

#### Explanation
This design represents standard web development practices:
* Sensitive state identifiers (session keys) are placed in secure `HttpOnly` cookies.
* Long-term user interface preferences (themes, language settings) are placed in `Local Storage`.
* Highly temporary, single-visit search parameters are stored in `Session Storage` to avoid cluttering memory.

---

### Assignment 3: JWT Token Authorization Flow

#### Solution Details
Analysis of the JWT validation receipt downloading flow:
```text
  [ Student Browser ] ─── 1. POST Credentials ───► [ Auth Server ]
  [ Student Browser ] ◄─── 2. Returns JWT Token ── [ Auth Server ]
          │
  (Stores Token in Session Storage)
          │
  [ Student Browser ] ─── 3. GET Receipt + Header ─► [ Receipt API ]
  [ Student Browser ] ◄─── 4. Returns PDF file ─── [ Receipt API ]
```

#### Answers to Requirements:
1. **The Three Parts of the JWT Token (from Step 2):**
   * **Header:** Holds token details (e.g., algorithm `HS256`, type `JWT`).
   * **Payload:** Contains client claims (e.g., student roll number, permissions, receipt download rights, expiration date).
   * **Signature:** Created by signing the header + payload using the server's private secret key. It detects client tampering.
2. **Exact HTTP Authorization Header Format (for Step 3):**
   * The token is passed as a **Bearer** token inside the `Authorization` header.
   * **Header format:**
     ```text
     Authorization: Bearer eyJ0b2tlbiI6InZqdGkifQ
     ```
3. **Behavior if Token is Tampered with:**
   * **HTTP Status Code Returned:** `401 Unauthorized` or `403 Forbidden`.
   * **Reason:** The signature is calculated dynamically by the backend using the header, payload, and a secret key. If a user manually alters the payload (e.g., changing their roll number to access another student's receipt), the calculated signature will not match the signature appended to the token. The server rejects the signature mismatch as invalid, resulting in access denial.

#### Explanation
This shows why JWT is secure:
* Even though the client stores the token in `Session Storage` (which is readable by JavaScript), they cannot forge it or change their identity attributes in the payload because the server validates the signature on every request.
* In automated API testing, we bypass UI login forms by issuing a credentials request, extracting the JWT string, and attaching the `Authorization: Bearer <token>` header to subsequent API client requests.
