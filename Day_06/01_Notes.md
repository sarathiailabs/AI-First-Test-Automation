# Day 6: Web Fundamentals for Testers

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Client-Server Architecture | 10 mins |
| Browser Architecture | 10 mins |
| HTTP & HTTPS | 10 mins |
| Request & Response | 10 mins |
| Status Codes | 10 mins |
| DOM (Document Object Model) | 10 mins |
| HTML Basics | 10 mins |
| CSS Basics | 10 mins |
| JavaScript Rendering | 10 mins |
| Cookies | 10 mins |
| Sessions | 5 mins |
| Local Storage & Session Storage | 5 mins |
| Authentication | 5 mins |
| JWT Tokens | 5 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Map network data flows between Clients (browsers) and Servers.
* Inspect HTTP/HTTPS request headers, response bodies, and payloads.
* Diagnose client-server errors using standard HTTP Status Codes.
* Traverse and manipulate web layouts using the DOM tree structure.
* Validate storage states (Cookies, Local Storage, Session Storage) inside browser panels.
* Inspect JWT Token authorization payloads to debug user session issues.

---

## Client-Server Architecture

### Definition
**Client-Server Architecture** is a distributed application structure that partitions tasks between resource providers (servers) and service requesters (clients). *(Client (browser) dwara request bhejna aur Server dwara response return karne ka system.)*

### Key Concepts
* **Client (Browser):** The user interface that sends requests (e.g., Google Chrome, Firefox).
* **Server:** The central computer that processes requests, runs business logic, and queries database systems.
* **Request-Response Cycle:** The client initiates a request, and the server responds. The server never initiates communication.

### Visual Explanation
Analogy: Ordering tea at a local Tapri.
```text
[ Customer (Client) ] ──── Orders Tea (Request) ────► [ Tea Tapri Waiter (Server) ]
                                                            │ (Prepares tea)
[ Customer (Client) ] ◄─── Serves Hot Tea (Response) ───────┘
```

### Example
#### Scenario
Logging into the VJTI Student ERP.
1. *Client:* Student enters roll number on their laptop browser and clicks submit.
2. *Network:* Request is sent over the internet to the college server.
3. *Server:* ERP server checks if the roll number is valid, fetches results from the database, and compiles a dashboard HTML file.
4. *Response:* Server sends the HTML file back to the student's browser.

### Real World Usage
In Playwright, we navigate to a client application which then communicates with back-end servers:
```javascript
await page.goto("https://vjti.ac.in"); // Client requests page from VJTI Server
```

### Common Mistakes
* **Assuming the client runs database queries directly:** The browser cannot query databases directly due to security blocks. It must ask the server via API requests.

### Topic Summary
Client requests resources; Server processes and returns responses.

---

## Browser Architecture

### Definition
**Browser Architecture** describes the structural components inside a web browser that work together to fetch, parse, and render web pages. *(Browser ke internal parts (UI, Rendering Engine, Network Layer) jo web page show karne me help karte hain.)*

### Key Concepts
* **User Interface:** The address bar, back/forward buttons, and tabs.
* **Browser Engine:** Coordinates actions between the UI and the rendering engine.
* **Rendering Engine:** Parses HTML and CSS to paint pixels on the screen (e.g. Blink in Chrome/Chromium, Gecko in Firefox, WebKit in Safari).
* **Network Layer:** Handles internet protocols (HTTP/HTTPS) and security.
* **JavaScript Interpreter:** Parses and executes JS scripts (e.g. V8 engine in Chrome).

### Visual Explanation
```text
┌───────────────────────────────────────────────┐
│ User Interface (Address bar, Tabs)            │
├───────────────────────────────────────────────┤
│ Browser Engine (Coordinates rendering)        │
├───────────────┬───────────────────────────────┤
│ Rendering     │ JavaScript Engine             │
│ Engine        │ (V8 Engine - Runs JS)         │
│ (Paints HTML) │                               │
└───────────────┴───────────────────────────────┘
```

### Real World Usage
Playwright supports testing across multiple browser rendering engines (Chromium, Firefox, and WebKit) using a single API wrapper:
```javascript
// Playwright launches Chromium rendering engine
const browser = await playwright.chromium.launch();
```

### Topic Summary
Browsers combine rendering engines, network layers, and JS engines to fetch and draw pages.

---

## HTTP & HTTPS

### Definition
**HTTP (Hypertext Transfer Protocol)** is the protocol used to transfer data over the web, while **HTTPS** is its secure, encrypted version using SSL/TLS certificates. *(Web par data transfer karne ke rules (HTTP) aur use SSL certificate se secure karne wala version (HTTPS).)*

### Key Concepts
* **Stateless Protocol:** Each request is independent. The server does not remember previous requests by default.
* **Encryption (HTTPS):** Encrypts data in transit, preventing hackers on public Wi-Fi from reading credentials or payment details.
* **SSL/TLS Handshake:** Secure key exchange established before transmitting page data.

### Visual Explanation
Analogy: Mailing a letter.
```text
HTTP:  Sending a postcard. Anyone at the post office (ISP, hackers) can read the text.
HTTPS: Placing the letter inside a locked steel lockbox (Encryption) that only the recipient can open.
```

### Example
* **Insecure:** `http://vjti.ac.in/login` (Credentials sent in plain text).
* **Secure:** `https://vjti.ac.in/login` (Credentials encrypted).

### Real World Usage
Ensuring your automated test URLs use `https` to match production security environments:
```javascript
await page.goto("https://vjti.ac.in"); // Secure connection
```

### Common Mistakes
* **Testing insecure staging environments expecting production behaviors:** Running payment tests over unencrypted HTTP might fail if APIs require HTTPS security.

### Topic Summary
HTTPS encrypts standard HTTP network packets, securing credentials and student data.

---

## Request & Response

### Definition
An **HTTP Request** is a data package sent by a client to trigger a server action, and an **HTTP Response** is the server's reply package containing the outcome data. *(Client se bheja gaya data package (Request) aur badle me server se aaya data package (Response).)*

### Key Concepts
* **Request Components:**
  * *Method (Verb):* GET (fetch), POST (create), PUT (update), DELETE (remove).
  * *URL:* Target destination path.
  * *Headers:* Metadata (Content-Type, Authorization tokens).
  * *Body (Payload):* Data being sent (e.g., login credentials).
* **Response Components:**
  * *Status Code:* Result summary (e.g., 200 OK).
  * *Headers:* Metadata (Content-Type, Set-Cookie).
  * *Body:* Returned data (HTML, JSON images).

### Syntax
**HTTP POST Request Header Example:**
```text
POST /api/v1/auth/login HTTP/1.1
Host: vjti.ac.in
Content-Type: application/json

{ "rollNo": 1005, "password": "securePass123" }
```

### Example
#### HTTP Response Header Example
```text
HTTP/1.1 200 OK
Content-Type: application/json
Set-Cookie: session_id=vjti-98765

{ "status": "Login Successful", "studentName": "Rahul Verma" }
```
#### Explanation
1. The client sends a `POST` request with the student's roll number payload.
2. The server authenticates, returns a `200 OK` status, and issues a session cookie.

### Real World Usage
Intercepting and mocking network responses in Playwright tests:
```javascript
await page.route('**/api/v1/auth/login', route => {
    route.fulfill({
        status: 200,
        contentType: 'application/json',
        body: JSON.stringify({ status: "Mocked Success" })
    });
});
```

### Topic Summary
Requests send methods and payloads; Responses return status codes and result bodies.

---

## Status Codes

### Definition
**HTTP Status Codes** are 3-digit numeric codes returned by a server indicating the completion status of a client's request. *(Server se aaye response ka 3-digit code jo transaction ka result (Success/Error) batata hai.)*

### Key Concepts
* **1xx (Informational):** Request received, processing continues.
* **2xx (Success):** Action completed successfully (e.g., `200 OK`, `201 Created`).
* **3xx (Redirection):** Further action needed (e.g., `301 Moved Permanently`, `302 Found`).
* **4xx (Client Error):** Bad request from client (e.g., `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`).
* **5xx (Server Error):** Server failed to complete a valid request (e.g., `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable`).

### Example
* **401 Unauthorized:** Trying to view fee invoices without logging in first.
* **404 Not Found:** Navigating to `https://vjti.ac.in/wrong-url-path`.
* **500 Server Error:** Database crashes during payment transaction processing.

### Real World Usage
Verifying network responses inside automated API test validations:
```javascript
const response = await page.request.post('https://vjti.ac.in/api/login');
expect(response.status()).toBe(200); // Verify success code
```

### Common Mistakes
* **Asserting general successes without checking codes:** A test script might pass even if the server returns a 500 error page if the locator finds the error text without verifying the status code.

### Topic Summary
Status codes categorize results: 2xx means success, 4xx means client mistakes, and 5xx represents server crashes.

---

## DOM (Document Object Model)

### Definition
The **DOM (Document Object Model)** is an API programming interface that represents HTML documents as a hierarchical tree structure, allowing scripts to dynamically update content and styling. *(HTML structure ko ek tree-like hierarchy me represent karne wala model jise JS manipulate kar sakta hai.)*

### Key Concepts
* **Tree Structure:** The document is represented as nodes (elements, attributes, text).
* **Root Node:** The `<html>` element acts as the root.
* **Dynamic Mutation:** JavaScript can locate, add, or delete DOM nodes dynamically.

### Visual Explanation
Analogy: College department hierarchy.
```text
             Document (Root)
                    │
                   HTML
             ┌──────┴──────┐
           Head          Body
             │             ├──────────────┐
           Title         Header          Main
                           │              │
                           H1           Button
```

### Example
#### HTML Source
```html
<div id="fees-card">
    <p class="amount">₹45000</p>
</div>
```
#### JavaScript DOM Manipulation
```javascript
// Locating a DOM node and changing its color styling
const feeAmount = document.querySelector("#fees-card .amount");
feeAmount.style.color = "green"; // Changes text color to green in browser UI
```

### Real World Usage
Playwright locates elements inside the DOM tree using CSS selectors or text matches:
```javascript
const payButton = page.locator("#fees-card >> button");
await payButton.click();
```

### Common Mistakes
* **Confusing the HTML Source with the Live DOM:** The source code is the initial text, whereas the DOM is the active, live tree in memory that dynamically updates as scripts run.

### Topic Summary
DOM is the active tree representing the web page. Testing tools locate nodes within this structure.

---

## HTML & CSS Basics

### Definition
* **HTML (Hypertext Markup Language)** defines the structure and elements of a web page, while **CSS (Cascading Style Sheets)** controls the design, visual layout, and styling.
* *(HTML: Web page ka core skeleton design karne ke tags; CSS: HTML structure ko style aur layout dene ka tarika.)*

### Key Concepts
* **Core HTML Elements:**
  * Inputs: `<input type="text">`, `<input type="checkbox">`
  * Buttons: `<button>`
  * Containers: `<div>`, `<span>`
  * Headings: `<h1>` to `<h6>`
* **Core CSS Selectors:**
  * ID Selector: `#login-btn` (targets `id="login-btn"`)
  * Class Selector: `.amount-field` (targets `class="amount-field"`)
  * Tag Selector: `button` (targets all `<button>` tags)

### Syntax
**HTML structure with inline CSS classes:**
```html
<div class="card">
    <h1 id="portal-title">ERP Login</h1>
    <input type="text" placeholder="Roll Number" />
    <button class="btn-submit">Submit</button>
</div>
```
**CSS File styling:**
```css
.card { padding: 20px; background-color: #f4f4f4; }
#portal-title { color: #002b49; }
```

### Real World Usage
Understanding HTML and CSS enables automation QAs to locate elements accurately without relying on flaky autofilled attributes:
```javascript
await page.fill('input[placeholder="Roll Number"]', "1005");
```

### Topic Summary
HTML sets structural content elements; CSS styles layouts and styles.

---

## JavaScript Rendering

### Definition
**JavaScript Rendering** is the process where the browser executes client-side scripts to dynamically build, modify, and display DOM elements. *(Browser engine dwara HTML/CSS ke sath client-side dynamic content draw karne ka process.)*

### Key Concepts
* **SSR (Server-Side Rendering):** The server compiles the complete HTML and ships it to the client browser.
* **CSR (Client-Side Rendering):** The server ships a blank HTML file along with a JS bundle. The browser runs the JS to build the UI elements dynamically.
* **Hydration:** Connecting event listeners to static HTML sent from server.

### Visual Explanation
```text
SSR: Server cooks food ──► Delivers completed meal ──► Client eats immediately.
CSR: Server delivers recipe & raw ingredients ──► Client cooks meal ──► Client eats.
```

### Real World Usage
CSR pages often load elements dynamically, requiring automated tests to wait for selectors to be drawn:
```javascript
// Wait for JS rendering engine to draw the table in the DOM:
await page.waitForSelector(".fees-table-row");
```

### Common Mistakes
* **Failing to wait for CSR elements:** Scripts that click elements immediately after navigation often fail on CSR sites because the element placeholder is present, but the actual dynamic content hasn't rendered yet.

### Topic Summary
JS Rendering dynamically draws elements inside the browser, requiring automated tests to wait for content to load.

---

## Cookies & Sessions

### Definition
* **Cookies** are small text files stored in the client browser by servers to retain state data, while **Sessions** are server-side records tracking active user identities.
* *(Cookies: Server dwara client ke browser me save kiya gaya small text data; Sessions: Server-side record jo user identity ko save rakhta hai.)*

### Key Concepts
* **HttpOnly Cookie:** A security attribute that prevents client-side JavaScript from reading cookie values, protecting sessions from XSS attacks.
* **Session Lifecycle:** The server associates a client's request with an active session using a `session_id` cookie passed automatically in HTTP headers.

### Visual Explanation
Analogy: Wardrobe locker key.
```text
1. Student deposits bag at hostel locker.
2. Warden returns a locker token key (Cookie: session_id=105).
3. Next time, student shows the token key to open their wardrobe drawer (Session State).
```

### Example
#### HTTP Cookie Exchange
```text
Client Login Request ──► Server Authenticates
Client Browser ◄── [Set-Cookie: session_id=vjti-98765] ── Server response
Next Request ──► [Cookie: session_id=vjti-98765] ──► Server matches ID in session database
```

### Real World Usage
Playwright lets us extract and inject cookie states to bypass login flows in regression test cycles:
```javascript
await context.addCookies([{ name: "session_id", value: "vjti-98765", domain: "vjti.ac.in", path: "/" }]);
```

### Common Mistakes
* **Trying to read HttpOnly cookies via JavaScript:** Document properties like `document.cookie` will omit `HttpOnly` values. Attempting to extract them using `page.evaluate()` will return empty.

### Topic Summary
Cookies store session IDs on the browser, allowing servers to identify returning users.

---

## Local Storage vs Session Storage

### Definition
**Browser Storage APIs** (Local and Session Storage) allow web applications to store key-value data directly inside the client's browser database. *(Browser database ya memory me key-value data store karne ke mechanisms.)*

### Key Concepts
* **Local Storage:**
  * *Lifespan:* Persistent. Data remains stored indefinitely even after the browser tab or browser app is closed.
  * *Capacity:* Around 5MB to 10MB.
* **Session Storage:**
  * *Lifespan:* Temporary. Data is wiped clean immediately when the browser tab is closed.
  * *Capacity:* Around 5MB.

### Comparison Table
| Feature | Local Storage | Session Storage | Cookies |
| --- | --- | --- | --- |
| **Duration** | Indefinite | Tab lifecycle only | Expiry date set |
| **Capacity** | 5-10 MB | 5 MB | 4 KB |
| **Sent to Server** | No | No | Auto on every request |

### Example
#### JavaScript Storage API Usage
```javascript
// Storing Student Theme Choice (Permanent)
localStorage.setItem("themePreference", "dark-mode");

// Storing temporary search input (Session only)
sessionStorage.setItem("currentSearchInput", "Data Structures");
```

### Real World Usage
Saving browser storage states in Playwright to preserve configuration settings between test files:
```javascript
// Capture storage state
await page.context().storageState({ path: 'state.json' });
```

### Topic Summary
Local Storage persists across browser restarts; Session Storage is wiped when tabs close.

---

## Authentication & JWT Tokens

### Definition
**Authentication** is the validation process verifying user identity, while a **JWT (JSON Web Token)** is a compact, digitally signed data packet used to securely transmit user claims between client and server. *(User verify karne ka process (Auth) aur verification ke liye use hone wala compact digitally signed data packet (JWT).)*

### Key Concepts
* **Basic Auth:** Sending raw base64 encoded username/password in headers (`Authorization: Basic base64_str`).
* **JWT Structure:** Composed of three parts separated by dots (`.`):
  1. *Header:* Algorithm type and token token properties.
  2. *Payload:* User claims (e.g. rollNo, name, role).
  3. *Signature:* Verification check signing the header and payload.
* **Bearer Token:** Sending the JWT back to the server inside request headers:
  `Authorization: Bearer <JWT_Token_String>`

### Visual Explanation
Analogy: Signed train ticket.
```text
1. Booking Clerk (Auth Server) validates ID, prints ticket with official signature stamp (JWT).
2. Passenger (Client) carries the ticket in their pocket (Browser Storage).
3. Ticket Checker (API Server) inspects the signature stamp to verify the ticket is valid,
   without calling the booking center database (Stateless validation).
```

### Example
#### JWT Token format in Auth Header
```text
GET /api/v1/student/receipt HTTP/1.1
Host: erp.vjti.ac.in
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xsTm8iOjEwMDUsIm5hbWUiOiJSYWh1bCJ9.SignatureHash
```

### Real World Usage
Injecting authorization header details inside Playwright API client tests:
```javascript
const apiContext = await playwright.request.newContext({
    extraHTTPHeaders: {
        'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...'
    }
});
```

### Common Mistakes
* **Storing JWT tokens unsafely:** Storing sensitive JWT tokens in Local Storage exposes them to XSS attacks. Store them in secure cookies if possible.

### Topic Summary
JWT tokens securely transmit signed user details in authorization headers, enabling stateless authentication.

---

## Session Summary

### Key Takeaways
1. **Network Flows:** Clients initiate requests, and servers return responses containing status codes (2xx success, 4xx/5xx errors).
2. **Web Layouts:** Browsers parse HTML content and CSS styling into the live DOM tree, executing JavaScript to render CSR content.
3. **Session States:** Session IDs are stored in HttpOnly cookies, while local storage persists configuration states across restarts.
4. **API Security:** Authentication tokens (JWT) are signed packages sent as Bearer tokens in HTTP headers to authenticate stateless transactions.

### Important Interview Points
* **What is the difference between Cookies, Local Storage, and Session Storage?**
  * Cookies are small (4KB) and sent to the server on every request. Local Storage (5MB+) persists indefinitely. Session Storage (5MB) is cleared when the tab closes.
* **What does `HttpOnly` mean when applied to a cookie?**
  * It prevents client-side scripts from reading the cookie, securing it against XSS attacks.
* **Explain the structure of a JWT Token.**
  * Composed of a Header (metadata), a Payload (user claims like username/ID), and a Signature (verifies the token's authenticity).
* **What is the difference between a 401 and a 403 status code?**
  * `401 Unauthorized` means the user is not logged in. `403 Forbidden` means the user is logged in but lacks permission to view the resource.
