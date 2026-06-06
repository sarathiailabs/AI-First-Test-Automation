# Day 6: Web Fundamentals for Testers – Student Revision Notes

Quick reference guide to revise the concepts covered in the Web Fundamentals for Testers session. Use this to review definitions, common pitfalls, and key points before coding or interviews.

---

## Client-Server Architecture

### Definition
**Client-Server Architecture** is a design where clients request services/resources, and a centralized server processes those requests and returns responses. *(Client request karta hai, server use process karke response deta hai).*

### Example
When you open a laptop and check your grades on the VJTI ERP portal:
* **Client:** Your Chrome browser.
* **Server:** The VJTI database and web application server located in the campus server room.

### Key Points
* The Client always initiates the communication.
* The Server waits for incoming requests, validates them, and sends a reply.
* Clients and servers do not connect directly to databases; servers act as secure gatekeepers.

### Common Mistakes
* **Thinking the client runs database code:** The browser only handles visual display and user clicks. It cannot run SQL queries directly.

---

## Browser Architecture

### Definition
**Browser Architecture** describes how different internal parts of a web browser (like the UI, rendering engine, network manager, and JS engine) work together to load a website. *(Browser ke internal parts jo visual display, network, aur script runtime ko manage karte hain).*

### Example
When you launch Chrome and visit a site, the **Rendering Engine** (Blink) paints the boxes, the **JavaScript Engine** (V8) runs the button-click code, and the **Network Layer** downloads the images.

### Key Points
* Different browsers use different rendering engines (Chromium/Blink, Firefox/Gecko, Safari/WebKit).
* The Rendering engine parses HTML and CSS. The JavaScript engine executes scripts.

### Common Mistakes
* **Assuming all browsers behave identically:** A selector might load perfectly on Chromium but fail on WebKit (Safari) due to slight differences in rendering engine timings. Always test cross-browser.

---

## HTTP & HTTPS

### Definition
**HTTP (Hypertext Transfer Protocol)** is the communication standard used to transfer web page data, while **HTTPS** is its secure version which uses encryption. *(Data transfer karne ka protocol (HTTP) aur use encrypt karke secure banane wala standard (HTTPS)).*

### Example
* **Insecure:** `http://vjti.ac.in/login` (Password is visible to hackers on the same Wi-Fi).
* **Secure:** `https://vjti.ac.in/login` (Password is scrambled into unreadable code during transport).

### Key Points
* HTTP is stateless; it treats each request as brand new.
* HTTPS uses SSL/TLS certificates to encrypt the request headers and bodies.

### Common Mistakes
* **Forgetting the 's' in test environments:** Navigating to `http://` instead of `https://` during test automation might lead to security blocks or redirects that fail your scripts.

---

## Request & Response

### Definition
An **HTTP Request** is a message sent by the client browser to request a resource or action, and an **HTTP Response** is the reply package sent back by the server. *(Client dwara bheji gayi request aur server se mila response).*

### Example
**Request Header:**
```text
POST /api/v1/auth/login HTTP/1.1
Host: erp.vjti.ac.in
Content-Type: application/json
```
**Response Header:**
```text
HTTP/1.1 200 OK
Content-Type: application/json
```

### Key Points
* A request contains a Method (GET, POST, etc.), URL, Headers (metadata), and optional Body (payload).
* A response contains a Status Code, Headers, and a Body (HTML content, JSON data).

### Common Mistakes
* **Sending payloads on GET requests:** Standard API design does not support sending body data inside a `GET` request. Use query parameters instead.

---

## Status Codes

### Definition
**HTTP Status Codes** are 3-digit numbers sent by the server indicating whether a request was successful, redirected, or failed. *(Server se aane wala status code jo request ka result batata hai).*

### Example
* `200 OK`: Request succeeded.
* `404 Not Found`: The requested page does not exist.
* `500 Internal Server Error`: The server program crashed.

### Key Points
* `2xx` = Success.
* `3xx` = Redirection.
* `4xx` = Client Error (your fault).
* `5xx` = Server Error (developer/server fault).

### Common Mistakes
* **Assuming status code 200 means no bugs:** A page can return `200 OK` but show a blank screen. Always verify the UI elements in addition to the status code.

---

## DOM (Document Object Model)

### Definition
The **DOM** is a tree-like hierarchy created by the browser that represents the structure of an HTML page, allowing JavaScript to read and modify elements. *(HTML structure ko element tree ke roop me represent karna jo dynamic modification allow karta hai).*

### Example
Visualizing a paragraph inside a card container:
```text
      [div (card)]
           │
     [p (amount)] ──► text: "₹45000"
```

### Key Points
* Every tag in HTML becomes a "Node" in the DOM tree.
* JavaScript dynamically reads, edits, or deletes elements using the DOM tree.

### Common Mistakes
* **Confusing raw HTML source code with the DOM:** HTML is the static text document loaded initially. The DOM is the live tree in memory that changes when elements are added or hidden via JavaScript.

---

## HTML Basics

### Definition
**HTML (Hypertext Markup Language)** is the standard markup language used to structure elements (buttons, inputs, texts) on a web page. *(Web page ka structure design karne ke tags).*

### Important Syntax
```html
<div class="form-container">
  <input type="text" id="username" placeholder="Enter Roll No" />
  <button id="login-btn">Login</button>
</div>
```

### Example
Creating a simple form with a text box and a submit button.

### Key Points
* HTML uses tags like `<div>`, `<input>`, `<button>`, `<p>`.
* Elements can have attributes like `id`, `class`, and `placeholder` which are used as locators in testing.

### Common Mistakes
* **Using duplicate IDs:** An `id` attribute must be unique on a page. If developers reuse IDs, automation tools like Playwright might select the wrong element or throw exceptions.

---

## CSS Basics

### Definition
**CSS (Cascading Style Sheets)** is the language used to apply styles, colors, and layout configurations to HTML elements. *(HTML elements ko color, design, aur style dene ki language).*

### Important Syntax
```css
#login-btn {
  background-color: green;
  color: white;
}
.error-msg {
  color: red;
}
```

### Example
Coloring the VJTI portal submit button green and text white.

### Key Points
* CSS targets HTML elements using selectors (e.g., `#` for ID, `.` for class).
* Testing frameworks use CSS selectors to pinpoint which button or input field to interact with.

### Common Mistakes
* **Relying on styling class names for locators:** Class names like `.btn-large-red-active` change easily during UI redesigns. Prefer stable attributes like `id` or data-attributes (e.g. `data-testid`).

---

## JavaScript Rendering

### Definition
**JavaScript Rendering** is the process where client-side JavaScript runs in the browser to dynamically build and paint the DOM. *(Browser me script execute karke dynamic UI banana).*

### Example
* **Server-Side Rendering (SSR):** Entering the portal and seeing the student profile page instantly.
* **Client-Side Rendering (CSR):** Opening a page and seeing a loading spinner while JavaScript downloads, runs, and builds the profile table.

### Key Points
* CSR is widely used in modern frameworks like React, Angular, and Vue.
* CSR requires automation scripts to wait for elements to render before clicking them.

### Common Mistakes
* **Clicking an element before rendering finishes:** Automation scripts running at high speeds will fail if they try to click a button that hasn't been rendered by JavaScript yet. Use explicit waits.

---

## Cookies

### Definition
**Cookies** are tiny text files containing key-value data saved by the browser, used mainly to store session keys. *(Browser me save kiya gaya small key-value data jo session track karta hai).*

### Example
```javascript
// Setting a cookie in JavaScript
document.cookie = "username=Rahul;";
```

### Key Points
* Cookies are automatically attached to every HTTP request sent to the server.
* They are limited to a small size of 4 KB.
* The `HttpOnly` flag stops client scripts from reading them, protecting against session hijacking.

### Common Mistakes
* **Trying to read HttpOnly cookies using custom page evaluation:** Trying to retrieve an `HttpOnly` cookie via `document.cookie` in standard browser JavaScript yields nothing. Use native Playwright APIs instead.

---

## Sessions

### Definition
A **Session** is a server-side record that stores data about an active visitor's status and login credentials. *(Server-side state record jo user identity verify karta hai).*

### Example
When you log into VJTI ERP, the server creates a session file with your profile data and sends a session ID back to your browser cookie. When you navigate to other pages, the server references this ID to check if you are still logged in.

### Key Points
* Sessions keep critical user data secure on the server.
* The client browser only holds the session ID key, not the actual data.

### Common Mistakes
* **Confusing Sessions with Cookies:** Cookies are the *storage method* on the browser; Sessions are the *state records* kept on the server.

---

## Local Storage

### Definition
**Local Storage** is a browser database API that stores permanent key-value pairs locally inside the client's browser. *(Browser ka permanent database store jo tab close karne par bhi delete nahi hota).*

### Important Syntax
```javascript
localStorage.setItem("theme", "dark");
let userTheme = localStorage.getItem("theme");
```

### Example
Storing user dark-mode choices permanently on the device.

### Key Points
* Large storage limit (approx. 5-10 MB).
* Data does not expire when the tab or browser is closed.
* Data is never sent to the server automatically.

### Common Mistakes
* **Storing sensitive tokens in Local Storage:** Local Storage has no security flags like `HttpOnly`. Any malicious script can read it, making it unsafe for passwords or raw session tokens.

---

## Session Storage

### Definition
**Session Storage** is a browser database API that stores temporary key-value data which is automatically deleted when the active tab is closed. *(Browser ka temporary database jo tab close hote hi clear ho jata hai).*

### Important Syntax
```javascript
sessionStorage.setItem("tempRollNo", "1005");
let roll = sessionStorage.getItem("tempRollNo");
```

### Example
Saving the room number selection page on the VJTI Hostel Portal while navigating forms in the current tab.

### Key Points
* Independent tab-level storage.
* Data is wiped out immediately when the tab is closed.
* Not shared across multiple browser tabs.

### Common Mistakes
* **Expecting data to persist across tabs:** If a user opens the portal in a new tab, Session Storage values from the first tab will not be available.

---

## Authentication

### Definition
**Authentication (Auth)** is the security process of verifying the identity of a client before allowing access to private resources. *(User identity verify karne ka security method).*

### Example
Entering your roll number and password, or scanning an OTP to verify that you are the actual student accessing the fee portal.

### Key Points
* Common models: Basic Auth (base64 username/password header), Session-Cookie Auth, and Token-Based Auth (JWT).
* Must be verified on every request to prevent unauthorized access.

### Common Mistakes
* **Assuming frontend protection is enough:** Hiding a button in HTML doesn't secure the endpoint. The server must authenticate the request payload directly.

---

## JWT Tokens

### Definition
A **JWT (JSON Web Token)** is a secure, digitally signed string used to transfer verification data (claims) between a client and server. *(Digitally signed token data jo client-server ke bich secure communication banata hai).*

### Important Syntax
Passed inside the HTTP Authorization Header:
```text
Authorization: Bearer <Your_JWT_Token_String>
```

### Example
An authenticated student downloading a grade sheet PDF by presenting a Bearer token in the request headers.

### Key Points
* Structure is `Header.Payload.Signature`.
* The Header specifies the algorithm; the Payload has user details; the Signature verifies integrity.
* Preferred in microservice systems because it is stateless (does not require database checks).

### Common Mistakes
* **Tampering with the token payload expecting success:** Changing the role field in the payload from `"student"` to `"admin"` will break the signature hash, causing the server to reject the token.

---

## Assignment Summary

* **Assignment 1: Analyze Login Request (Network Log Parsing)**
  * *Concepts Practiced:* Analyzing HTTP methods, request headers, payload values, and response cookie mechanisms in VJTI portals.
* **Assignment 2: Inspect Browser Storage (ERP Theme & Sessions)**
  * *Concepts Practiced:* Distinguishing between Cookies, Local Storage, and Session Storage behavior, lifetimes, and security scopes.
* **Assignment 3: JWT Token Authorization Flow**
  * *Concepts Practiced:* Understanding JWT components (Header, Payload, Signature), Bearer token request formatting, and signature verification errors.

---

## Quick Revision Sheet

| Concept | Lifetime | Capacity | Sent to Server? | Primary Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Cookies** | Expire time set by server | ~4 KB | Yes, on every call | Session identifiers, tracking |
| **Local Storage** | Permanent | ~5-10 MB | No | User UI preferences, themes |
| **Session Storage** | Cleared on tab close | ~5 MB | No | Temporary page form drafts |
| **JWT Header** | N/A | Variable | Yes, as Bearer header | Hashing algorithm used |
| **JWT Payload** | N/A | Variable | Yes, as Bearer header | User claims (ID, email, roles) |
| **JWT Signature** | N/A | Variable | Yes, as Bearer header | Tamper detection, security |

---

## Important Takeaways

1. **Automation Strategy:** Understanding browser storages helps QAs load pre-saved authorization sessions to bypass repeated login UI flows during testing, saving execution time.
2. **Network Integrity:** Every UI action is backed by an HTTP Request-Response cycle. Inspecting network logs (DevTools Network Tab) is crucial to pinpoint whether a test failure is a frontend display bug (CSR/DOM issue) or a backend server crash (500 status code).
3. **Security Standards:** Secure cookies (`HttpOnly`) and signed tokens (`JWT`) are the backbone of modern web app security. Automated scripts must be configured to pass Bearer authentication headers directly to simulate logged-in user requests.
