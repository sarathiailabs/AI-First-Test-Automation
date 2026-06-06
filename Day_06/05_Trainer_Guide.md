# Day 6: Web Fundamentals for Testers – Trainer Teaching Guide

This guide is designed for instructors delivering the "Web Fundamentals for Testers" session. It details the lecture scripts, engagement strategies, whiteboard sketches, live DevTools/coding demonstrations, and common student struggles.

---

## Session Opening

### Welcome Script
"Hello everyone! Welcome to Day 6 of our Playwright Automation Bootcamp. Before we write automated tests, we need to understand *what* we are testing. If we don't understand how websites, servers, and browsers talk to each other under the hood, writing automation scripts will feel like magic, and we will constantly struggle with flakiness and locator errors. Today, we are going to demystify the web."

### Session Goal
By the end of today's 2-hour session, you will confidently inspect network traffic, analyze status codes, locate DOM elements like a pro, differentiate browser storages, and understand how modern APIs verify who you are using JWT tokens. This is the foundation of both API testing and robust UI automation.

### Motivation
Imagine this: It is the day of VJTI fee payment. Thousands of students log in at the same time. The page spins, displays a loading spinner, and then shows a blank screen or a "502 Bad Gateway" error.
* As a QA engineer, how do you debug this?
* Is it because the server crashed?
* Or did the browser fail to load the payment button?
Today's session gives you the tools to open the browser console and point exactly to where the breakdown happened.

---

## 1. Client-Server Architecture

### Trainer Introduction
"Let's start at the very beginning. When you type `google.com` or `erp.vjti.ac.in` in your browser and press Enter, what actually happens? The browser on your laptop doesn't have all the college databases or student records stored on it. Your browser is just a 'Client'. It must ask another machine—the 'Server'—to get that information. The server processes the request and sends the response. Think of it like going to a restaurant and ordering food."

### Student Engagement Questions
1. When you check your exam results on your mobile phone, is the result data stored inside your phone's memory?
2. If the college server is shut down for maintenance, can your browser still load your student dashboard? Why or why not?
3. Can a client make changes directly to the server's database?

### Whiteboard Teaching
Draw the client-server request-response loop:
```text
  [ Client (Your Laptop/Chrome) ] ────── 1. Request: "Get my fees" ────► [ Server (VJTI Computer) ]
                                                                                │ (Fetches database)
  [ Client (Your Laptop/Chrome) ] ◄───── 2. Response: "HTML/Receipt" ───────────┘
```
Explain the analogy: The client is the customer at a restaurant table. The server is the kitchen. The internet is the waiter carrying the order back and forth. The customer never walks into the kitchen to cook or grab ingredients directly!

### Teaching Flow
1. Introduce the problem: Data needs to be retrieved securely from a centralized database.
2. Define Client and Server.
3. Draw the Request-Response cycle.
4. Explain that the server acts as the gatekeeper.
5. Emphasize that the client *always* initiates the loop.

### Live Coding Demonstration
Instead of coding, perform a browser inspection walkthrough:
#### Step 1
Open Google Chrome and navigate to `https://erp.vjti.ac.in` (or any public landing page).
#### Step 2
Right-click anywhere on the page and select **Inspect** to open DevTools. Click on the **Network** tab.
#### Step 3
Press `F5` to reload the page. Point out the list of files loaded (HTML, images, stylesheets).
#### Step 4
Show the students that each row represents a separate Request-Response transaction.
#### Step 5
**Student Exercise:** Have students open their own browser DevTools, go to a site of their choice, and look at the number of requests loaded in the status bar at the bottom.

### Common Student Doubts
#### Student Question
"Can the server send an update to my browser without me requesting it?"
#### Trainer Response
"In basic HTTP client-server architecture, no. The client must always ask first. If the client doesn't request, the server remains silent. (For real-time chats, we use advanced protocols like WebSockets, but standard web pages are strictly pull-based)."

### Real World Discussion
In automation, Playwright acts as the client agent. It automates user interactions (clicks, fills) which trigger HTTP requests to the backend server. If the server is slow, the client test script must wait for the response.

### Interview Talking Points
* **Interview Question:** "What is the role of a client vs a server?"
* **Key Terminology:** *Request-Response Cycle*, *Stateless Connection*, *Centralized Data Host*.

### Topic Recap
* **Definition:** Client asks; Server answers.
* **Key Takeaway:** The client never talks to the database directly; it requests data through the server.
* **Interview Point:** Standard HTTP is client-driven and stateless.

### Transition Script
"Now that we know the client browser must talk to the server, let's look at the internal components of the browser client that make this happen."

---

## 2. Browser Architecture

### Trainer Introduction
"A web browser is not just a simple window. It is a highly complex application. Under the hood, it contains a rendering engine to draw the page, a network layer to handle internet security, and a JavaScript interpreter to execute scripts. When we automate tests using Playwright, we are communicating directly with these internal engines."

### Student Engagement Questions
1. Why do some websites look slightly different when opened in Chrome versus Safari on an iPhone?
2. What part of the browser handles security certificates (HTTPS)?
3. What is the V8 engine? Have you heard of it?

### Whiteboard Teaching
Draw the block diagram of browser internals:
```text
┌─────────────────────────────────────────────────────────┐
│ User Interface (Address Bar, Back button, Tabs)        │
├─────────────────────────────────────────────────────────┤
│ Browser Engine (Coordinates rendering and UI)          │
├────────────────────────────┬────────────────────────────┤
│ Rendering Engine (Paints)  │ JavaScript Engine (Runs)   │
│ - Blink (Chrome, Edge)     │ - V8 (Chrome)              │
│ - Gecko (Firefox)          │ - SpiderMonkey (Firefox)   │
│ - WebKit (Safari)          │ - JavaScriptCore (Safari)  │
└────────────────────────────┴────────────────────────────┘
```

### Teaching Flow
1. Outline the main layers: UI, Browser Engine, Rendering Engine, JS Engine, and Network Layer.
2. Explain the role of the Rendering Engine (converting HTML/CSS to visual pixels).
3. Introduce the JS Engine (interpreting JavaScript code).
4. List the major browser engine families (Blink, Gecko, WebKit).

### Live Coding Demonstration
#### Step 1
Explain that Playwright can launch headless versions of these engines. Show a quick script:
```javascript
const { chromium, firefox, webkit } = require('playwright');
// We can run tests across three distinct engines!
```
#### Step 2
Explain that Chromium powers Chrome and Edge, WebKit powers Safari, and Firefox uses Gecko.
#### Step 3
Explain how cross-browser bugs happen when a rendering engine interprets CSS rules differently.

### Common Student Doubts
#### Student Question
"If I install Playwright, do I need to download Chrome, Firefox, and Safari separately?"
#### Trainer Response
"No! Playwright automatically downloads clean, isolated builds of the Chromium, Firefox, and WebKit browser engines during installation, so your tests run in standard environments without relying on whatever browser version you have installed."

### Real World Discussion
A QA must test on all three engines because a layout styling that passes on Chrome (Chromium) might look broken or overlap on Safari (WebKit).

### Interview Talking Points
* **Interview Question:** "What rendering engine does Chrome/Safari/Firefox use?"
* **Key Terminology:** *Rendering Engine*, *Blink*, *WebKit*, *Gecko*, *JavaScript Interpreter*.

### Topic Recap
* **Definition:** Browser architecture divides the app into UI, Network, Rendering, and JS Engine layers.
* **Takeaway:** Cross-browser testing verifies rendering engine consistency.

### Transition Script
"Since the browser network layer handles internet data traffic, let's learn about the language it speaks: HTTP and its secure sibling HTTPS."

---

## 3. HTTP & HTTPS

### Trainer Introduction
"To send requests over the wire, clients and servers must agree on a set of rules. This standard is HTTP (Hypertext Transfer Protocol). But raw HTTP is like sending a postcard through the post office—anyone along the path can read your data. That's why we use HTTPS, which encrypts the letters inside a secure envelope before mailing them."

### Student Engagement Questions
1. Why does your browser display a green padlock icon next to some URLs and a 'Not Secure' warning next to others?
2. If you enter your password on an `http://` page, who can see it?
3. What is the SSL/TLS handshake in simple terms?

### Whiteboard Teaching
Draw HTTP vs HTTPS:
```text
HTTP (Insecure):
  [ Client ] ─── Password: "myPass123" (Plain Text) ───► [ Router ] ──► [ Server ]
                                                            ▲ (Hackers read it)

HTTPS (Secure):
  [ Client ] ─── Password: "x98@!#$kL" (Encrypted) ────► [ Router ] ──► [ Server ]
                                                            ▲ (Hackers read gibberish)
```

### Teaching Flow
1. Explain that HTTP is a plain-text protocol.
2. Highlight the security vulnerability of plain-text data.
3. Introduce HTTPS (HTTP + SSL/TLS).
4. Explain encryption: scrambling data with keys so only the server can read it.
5. Emphasize why automation environments must match production HTTPS configurations.

### Live Coding Demonstration
#### Step 1
Navigate to `http://example.com` (plain HTTP).
#### Step 2
Click on the info icon in the address bar to show students the "Your connection to this site is not secure" warning.
#### Step 3
Now navigate to `https://vjti.ac.in` and show the locked padlock indicating a valid certificate.
#### Step 4
Show the SSL certificate details by clicking the certificate viewer in the browser security tab.

### Common Student Doubts
#### Student Question
"If HTTPS encrypts the request, does it mean my automation tests cannot inspect the request details?"
#### Trainer Response
"No. The encryption happens *after* the browser network layer sends it out, and decryption happens on the server. Since your DevTools and Playwright scripts run *inside* the browser context, they can read the headers and payloads in plain text before encryption."

### Real World Discussion
Security protocols might reject API requests that use invalid SSL certificates. In Playwright, we can configure our tests to bypass SSL check errors in local staging environments:
```javascript
// config: { ignoreHTTPSErrors: true }
```

### Interview Talking Points
* **Interview Question:** "What is the difference between HTTP and HTTPS?"
* **Key Terminology:** *Encryption*, *SSL/TLS Certificate*, *Man-in-the-Middle Attack*.

### Topic Recap
* **Definition:** HTTPS is the secure, encrypted version of HTTP.
* **Rule:** Always use secure endpoints for sensitive login/payment flows.

### Transition Script
"Now that we know the medium is secure, let's look at the structure of the data package we send: the HTTP Request and Response."

---

## 4. Request & Response

### Trainer Introduction
"Every communication between client and server is divided into a Request and a Response. If you want to log in, you send a POST request with your credentials. The server inspects them and returns a response containing success status or errors. Let's look at what headers and payloads look like."

### Student Engagement Questions
1. If you want to retrieve your student details, which HTTP method should you use? (GET or POST?)
2. What is the purpose of the `Content-Type` header?
3. Where is the actual login password sent in a POST request? (Headers or Body?)

### Whiteboard Teaching
Draw the anatomy of HTTP structures:
```text
┌──────────────────────────────────────────────┐
│ HTTP REQUEST                                 │
│  - Method: POST                              │
│  - Path: /api/login                          │
│  - Headers: Content-Type: application/json   │
│  - Body: { "user": "rahul" }                 │
└──────────────────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│ HTTP RESPONSE                                │
│  - Status Code: 200 OK                       │
│  - Headers: Set-Cookie: session=123          │
│  - Body: { "status": "welcome" }             │
└──────────────────────────────────────────────┘
```

### Teaching Flow
1. Explain Request components: Method, Path/URL, Headers, and Body.
2. Discuss HTTP methods: GET (fetch data), POST (create/submit data), PUT (update), DELETE (remove).
3. Explain Response components: Status Code, Headers, and Body.
4. Show raw text formatting examples.

### Live Coding Demonstration
#### Step 1
Open Chrome DevTools, select the **Network** tab, and filter by **Fetch/XHR** requests.
#### Step 2
Submit a mock form or navigate to a portal. Click on a network request from the list.
#### Step 3
Point out the **Headers** tab in DevTools: General (URL, Method, Status), Request Headers, and Response Headers.
#### Step 4
Click the **Payload** tab to show the raw JSON body being sent.
#### Step 5
Click the **Preview/Response** tab to show the JSON data returned by the server.

### Common Student Doubts
#### Student Question
"What is the difference between a header and a body payload?"
#### Trainer Response
"Headers are like metadata written on the outside of an envelope (e.g., recipient address, content type, letter size). The body payload is the actual letter contents inside the envelope (e.g., your credentials or receipt file)."

### Real World Discussion
When writing automated API tests, you construct these requests programmatically:
```javascript
const response = await request.post('/api/v1/auth/login', {
  data: { studentRollNo: 1005 }
});
```

### Interview Talking Points
* **Interview Question:** "What are the core components of an HTTP request?"
* **Key Terminology:** *Headers*, *Payload/Body*, *HTTP Methods (GET/POST/PUT/DELETE)*.

### Topic Recap
* **Definition:** Request is the query packet; Response is the answer packet.
* **Rule:** Use GET for retrieving data and POST for sending sensitive/state-changing data.

### Transition Script
"The very first indicator of success or failure in an HTTP Response is the 3-digit Status Code. Let's look at the different status code families."

---

## 5. Status Codes

### Trainer Introduction
"When the server returns a response, it includes a 3-digit status code. This is the server's way of saying: 'Yes, I did it' (200s), 'Go look over there' (300s), 'You made a mistake' (400s), or 'Oops, I crashed' (500s). As a tester, asserting these codes is your first line of defense."

### Student Engagement Questions
1. Have you ever seen a "404 Not Found" error while browsing? What did you do?
2. What status code family does "500 Internal Server Error" belong to? Who is responsible for fixing it?
3. If you type the wrong password, what code do you expect?

### Whiteboard Teaching
Draw the status code scale:
```text
  1xx: Informational (Processing...)
  2xx: Success (200 OK, 201 Created)
  3xx: Redirection (301 Moved, 302 Found)
  4xx: Client Error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found)
  5xx: Server Error (500 Server Error, 502 Bad Gateway, 503 Service Unavailable)
```

### Teaching Flow
1. Introduce the 5 standard categories.
2. Detail common QA-relevant codes: `200`, `201`, `400`, `401`, `403`, `404`, and `500`.
3. Provide real-world testing scenarios for each.
4. Explain how to assert status codes in scripts.

### Live Coding Demonstration
#### Step 1
Write a small test assert snippet:
```javascript
const response = await page.request.get('https://erp.vjti.ac.in/api/v1/missing-page');
expect(response.status()).toBe(404); // Assert page not found
```
#### Step 2
Discuss how asserting status codes catches server crashes early, even if the UI still displays a loading circle.

### Common Student Doubts
#### Student Question
"What is the difference between 401 Unauthorized and 403 Forbidden?"
#### Trainer Response
"401 means you haven't shown your identity (you are anonymous or not logged in). 403 means we know who you are, but you do not have permission to view that resource (e.g., a student trying to view the principal's admin panel)."

### Real World Discussion
If you run automated tests and see a surge of 500 errors, the developer's backend API code is crashing or database connections are failing. If you see 400 errors, your test script is sending badly formatted payloads.

### Interview Talking Points
* **Interview Question:** "What do status code ranges 2xx, 4xx, and 5xx represent?"
* **Key Terminology:** *Client Error*, *Server Error*, *Status Assertions*.

### Topic Recap
* **Definition:** 3-digit numeric codes representing HTTP response statuses.
* **Rule:** Validate status codes in test assertions before verifying the response body structure.

### Transition Script
"Once the browser client receives a successful HTML response, it builds the page structure inside memory using the DOM. Let's study how the DOM tree works."

---

## 6. DOM (Document Object Model)

### Trainer Introduction
"When the browser downloads an HTML file, it doesn't just read it like a text document. It parses it into a living tree structure in memory called the DOM (Document Object Model). Every button, input, and paragraph is a node in this tree. JavaScript and testing tools like Playwright interact with this tree to automate clicks and extract text."

### Student Engagement Questions
1. Is the HTML code you write in a editor the exact same thing as the live web page you interact with?
2. If JavaScript deletes a paragraph when a button is clicked, does the source file on the server change?
3. How does Playwright locate elements? What model does it search?

### Whiteboard Teaching
Draw a basic DOM tree:
```text
             [document]
                 │
               [html]
         ┌───────┴───────┐
       [head]          [body]
         │               ├───────────────┐
      [title]         [div#card]      [script]
                         │
                      [p.price]
```
Show how changing `p.price.innerText = "₹50"` updates only the memory node, changing the screen display dynamically without modifying the code on the disk.

### Teaching Flow
1. Define the DOM.
2. Explain node hierarchy (Parents, Children, Siblings).
3. Contrast raw HTML source file vs the memory-resident DOM tree.
4. Show how locator tools traverse the DOM tree.

### Live Coding Demonstration
#### Step 1
Open browser DevTools on any page. Select the **Console** tab.
#### Step 2
Type `document.body.style.backgroundColor = 'lightblue';` and press Enter.
#### Step 3
Watch the page background turn light blue instantly.
#### Step 4
Show students that the HTML file wasn't modified on the server; we only manipulated the live DOM node in the browser's memory.
#### Step 5
**Student Exercise:** Have students change the title text of a web page on their local browser using `document.title = 'My New Title'`.

### Common Student Doubts
#### Student Question
"Why does Playwright use locators instead of raw JavaScript query selectors?"
#### Trainer Response
"Playwright locators wrap around the DOM nodes and provide advanced testing features like auto-waiting, retrying on actionability checks, and searching through Shadow DOMs, which raw JS query selectors do not do automatically."

### Real World Discussion
If a dynamic UI update takes a split second, the node is not immediately present in the DOM tree. Playwright will retry searching the DOM tree up to a timeout limit before throwing an error.

### Interview Talking Points
* **Interview Question:** "What is the DOM and how does it differ from HTML?"
* **Key Terminology:** *DOM Tree*, *Node Elements*, *Memory Representation*.

### Topic Recap
* **Definition:** The DOM is the browser's in-memory object representation of the page structure.
* **Takeaway:** JavaScript manipulates the DOM; automated tests locate elements within the DOM.

### Transition Script
"The DOM is built from two foundational building blocks: HTML for structure and CSS for styling. Let's review these basics next."

---

## 7. HTML & CSS Basics

### Trainer Introduction
"To locate elements efficiently in Playwright, you must understand HTML and CSS. Think of HTML as the skeleton of the website (bones, structure) and CSS as the skin and clothing (colors, spacing, look). If you can read HTML attributes and CSS selectors, writing locator strategies becomes effortless."

### Student Engagement Questions
1. What is the difference between an element `id` and a `class`?
2. Which CSS selector character represents an ID? Which represents a class?
3. Can an element have multiple classes? Can it have multiple IDs?

### Whiteboard Teaching
Draw selector structures:
```text
  HTML:
  <input type="text" id="student-name" class="form-input active" />

  CSS Target Selectors:
  - ID Selector:    #student-name
  - Class Selector:  .form-input  or  .active
  - Attribute Selector: input[type="text"]
```

### Teaching Flow
1. Review core HTML tags: `div`, `p`, `button`, `input`, `select`.
2. Explain key attributes: `id` (unique), `class` (reusable styling), `name`, `placeholder`, and custom attributes.
3. Review CSS Selectors: Tag selectors, class selectors (`.`), ID selectors (`#`), and combined selectors.

### Live Coding Demonstration
#### Step 1
Open DevTools, select the **Elements** tab.
#### Step 2
Click the inspect tool (top-left mouse icon) and hover over a search input box.
#### Step 3
Show the highlight code line in the DOM inspector panel.
#### Step 4
Press `Ctrl + F` inside the Elements panel to open the selector search bar.
#### Step 5
Type CSS selectors like `#username` or `input.form-control` to show how Chrome highlights matching nodes.

### Common Student Doubts
#### Student Question
"Should I use class names or IDs to find elements in my automation tests?"
#### Trainer Response
"Always prefer IDs (`#student-name`) because they are unique and stable. Class names (`.btn-red-large`) are for styling and can change easily when a designer updates the theme, which would break your tests."

### Real World Discussion
Best practice in modern QA is to ask developers to add dedicated test attributes like `data-testid="submit-login"` to ensure automation scripts do not break during class updates or style overhauls.

### Interview Talking Points
* **Interview Question:** "What CSS selector would you write to locate a button with class 'primary' inside a div with ID 'login'?"
* **Key Terminology:** *ID selector*, *Class selector*, *Attribute selector*, *data-testid*.

### Topic Recap
* **Definition:** HTML structures content; CSS applies styles.
* **Rule:** Use unique IDs or custom data attributes as target locators for automated tests.

### Transition Script
"With structural HTML and CSS styles ready, the browser needs to draw the elements. Let's see how JavaScript rendering engines change when and how these elements appear on screen."

---

## 8. JavaScript Rendering

### Trainer Introduction
"In the early days of the web, the server did all the work (Server-Side Rendering or SSR). It sent a fully completed HTML page. Today, modern apps use Client-Side Rendering (CSR). The server sends a blank page and a large JavaScript file. The browser runs this script to draw elements on the fly. As a tester, this distinction is critical because CSR apps require dynamic waits."

### Student Engagement Questions
1. Why do some websites show layout outlines or spinners for a split second before actual text loads?
2. What is the difference between cooking a meal at a restaurant (SSR) vs buying a ready-to-make meal kit (CSR)?
3. If an element isn't in the DOM yet, what happens when a script clicks it?

### Whiteboard Teaching
Draw SSR vs CSR loading timelines:
```text
SSR:
  Server compiles HTML ──► Ships complete HTML ──► Browser paints page instantly.

CSR:
  Server sends blank HTML + JS ──► Browser loads JS ──► JS fetches API data ──► JS renders DOM.
```

### Teaching Flow
1. Explain SSR: Server compiles page, fast initial load, good for simple content.
2. Explain CSR: Client browser builds page dynamically, loading spinners, highly interactive.
3. Discuss the testing challenges of CSR (timing issues, waiting states).
4. Explain how Playwright handles this via locator auto-waiting.

### Live Coding Demonstration
#### Step 1
Show a simple script showing what happens without waiting in a CSR environment:
```javascript
await page.goto("https://erp.vjti.ac.in/dashboard");
// Without auto-waiting, this fails if dashboard is client-rendered:
await page.click("#fees-button");
```
#### Step 2
Demonstrate how Playwright's locator API resolves this by waiting for element visibility automatically:
```javascript
// Playwright automatically waits up to 30 seconds for #fees-button to attach
await page.locator("#fees-button").click();
```
#### Step 3
Show how to explicitly wait for loading states to disappear.

### Common Student Doubts
#### Student Question
"If Playwright auto-waits, why do my tests still fail occasionally with ElementNotFound?"
#### Trainer Response
"Playwright waits for the element to appear in the DOM, but sometimes the element is technically present but disabled or covered by a transparent loading spinner. In those cases, you must add explicit wait actions for the spinner to detach."

### Real World Discussion
Many React or Angular applications render components asynchronously, meaning elements arrive in the DOM tree at different milliseconds depending on network speeds. Your test suite must be resilient to these timing variations.

### Interview Talking Points
* **Interview Question:** "What is CSR and how does it affect test automation?"
* **Key Terminology:** *Server-Side Rendering*, *Client-Side Rendering*, *Hydration*, *Auto-waiting*.

### Topic Recap
* **Definition:** CSR builds the UI dynamically inside the browser memory.
* **Rule:** Always design test scripts to wait for target elements to load.

### Transition Script
"To make websites feel personalized and keep users logged in, browsers must store state. Let's explore the first state-saving mechanism: Cookies and Sessions."

---

## 9. Cookies & Sessions

### Trainer Introduction
"HTTP is stateless—it has short-term amnesia. It doesn't remember who you are from one click to the next. To solve this, servers use Sessions to store user records, and browsers use Cookies to store a session key. The browser automatically carries this key in its pocket on every request, proving to the server that you are logged in."

### Student Engagement Questions
1. Why don't you have to type your password again every time you click on a new link inside the student portal?
2. If you copy a cookie value from your logged-in browser and paste it into another computer's browser, what happens?
3. What is the role of the `HttpOnly` security flag?

### Whiteboard Teaching
Draw the Session-Cookie lifecycle:
```text
  [ Browser ] ─── 1. Login credentials ───► [ Server ] (Validates)
  [ Browser ] ◄─── 2. Cookie: session=99 ─── [ Server ] (Saves session=99 in DB)

  On next click:
  [ Browser ] ─── 3. Request + Cookie:99 ──► [ Server ] (Looks up session 99)
  [ Browser ] ◄─── 4. Success: Result page ─ [ Server ]
```

### Teaching Flow
1. Explain statelessness and the need for persistent session states.
2. Define Cookies (client-side text storage) and Sessions (server-side record).
3. Walk through the Set-Cookie response header and Cookie request header cycle.
4. Highlight the `HttpOnly` flag as an anti-hijack guard.

### Live Coding Demonstration
#### Step 1
Open browser DevTools, click on the **Application** tab.
#### Step 2
In the left sidebar, expand **Cookies** and click the current domain website.
#### Step 3
Point out the columns: Name, Value, Domain, Path, Expiry, and the **HttpOnly** / **Secure** checkboxes.
#### Step 4
Show how to delete a cookie manually in DevTools and refresh the page to show that the user is logged out immediately.

### Common Student Doubts
#### Student Question
"Can I read HttpOnly cookies from my Playwright automation script?"
#### Trainer Response
"Yes! Even though client-side JavaScript (`document.cookie`) is blocked, Playwright interacts directly with the browser's developer protocol, letting you retrieve, save, or inject all cookies securely."

### Real World Discussion
In automation testing, logging in via the UI before every test file takes too much time. Instead, we log in once, capture the cookie state, and inject those cookies into browser contexts before subsequent tests run.

### Interview Talking Points
* **Interview Question:** "How do cookies and sessions work together?"
* **Key Terminology:** *Set-Cookie Header*, *HttpOnly*, *XSS Vulnerabilities*.

### Topic Recap
* **Definition:** Cookies store session tokens locally; Sessions keep identity state on the server.
* **Takeaway:** Deleting the session cookie logs the user out.

### Transition Script
"While cookies are great for session IDs, modern browsers also have local databases to store larger volumes of UI preferences. Let's compare Local Storage and Session Storage."

---

## 10. Local Storage vs Session Storage

### Trainer Introduction
"Web browsers provide two other key-value storage mechanisms: Local Storage and Session Storage. These are larger than cookies (5MB+ vs 4KB) and are never sent to the server automatically. Local Storage lasts forever, while Session Storage disappears the moment you close the browser tab."

### Student Engagement Questions
1. If you set a theme to 'Dark Mode' on a site, close the browser, and reopen it next week, why does it remember your choice?
2. If you open a hostel booking search form in two different browser tabs, should they overwrite each other's search results? Which storage prevents this?
3. Why shouldn't you save a login password in Local Storage?

### Whiteboard Teaching
Draw a storage comparison grid:
```text
┌─────────────────┬───────────────────────────────┬───────────────────────────────┐
│ Feature         │ Local Storage                 │ Session Storage               │
├─────────────────┼───────────────────────────────┼───────────────────────────────┤
│ Lifespan        │ Permanent (Until cleared)     │ Temporary (Tab close)         │
│ Scope           │ Shared across all tabs        │ Isolated to active tab        │
│ Size            │ ~5-10 MB                      │ ~5 MB                         │
│ Sent to Server  │ No                            │ No                            │
└─────────────────┴───────────────────────────────┴───────────────────────────────┘
```

### Teaching Flow
1. Introduce HTML5 Web Storage APIs.
2. Detail Local Storage behavior and lifespans.
3. Detail Session Storage isolation.
4. Contrast both with Cookies (size, network transmission).
5. Highlight security concerns (Local Storage is accessible by client JavaScript).

### Live Coding Demonstration
#### Step 1
Open DevTools, select the **Console** tab.
#### Step 2
Type `localStorage.setItem('portalTheme', 'dark');` and press Enter.
#### Step 3
Navigate to the **Application** tab, expand **Local Storage**, and show the key-value pair.
#### Step 4
Type `sessionStorage.setItem('tempRollNo', '1005');` in the console, and show the entry in **Session Storage** in the Application panel.
#### Step 5
**Student Exercise:** Close the browser tab, open a new tab, and check if both items are still visible in DevTools. (Local Storage stays; Session Storage is empty).

### Common Student Doubts
#### Student Question
"If Local Storage data is never sent to the server, why do we use it?"
#### Trainer Response
"We use it to save UI preferences, page states, or draft forms locally. This avoids calling the server database repeatedly, saving server load and making the application feel faster."

### Real World Discussion
QA automation scripts can check local storage variables to assert that UI settings (like font sizes, collapsed sidebars, or unsubmitted drafts) persist correctly.

### Interview Talking Points
* **Interview Question:** "What is the difference between Cookies, Local Storage, and Session Storage?"
* **Key Terminology:** *Tab Lifecycle*, *Browser Persistence*, *Storage Limits*.

### Topic Recap
* **Definition:** Browser key-value storage APIs. Local storage is persistent; Session storage is tab-temporary.
* **Rule:** Never store passwords, API tokens, or personal identifiers in raw Local Storage.

### Transition Script
"Finally, let's explore token-based authentication, which is the standard for modern APIs: JSON Web Tokens (JWT)."

---

## 11. Authentication & JWT Tokens

### Trainer Introduction
"When we build modern REST APIs or distributed microservices, traditional session databases become slow. Instead, we use JSON Web Tokens (JWT). A JWT is like a digitally signed train ticket. The client stores it, and whenever they make an API request, they show the ticket. The server verifies the signature stamp instantly without checking a database."

### Student Engagement Questions
1. What does 'Stateless Authentication' mean?
2. If you buy a train ticket from the station agent, does the train ticket checker call the office database to verify your ticket? How do they check it?
3. What happens if a hacker edits the data inside a JWT token payload?

### Whiteboard Teaching
Draw JWT structure and authorization header format:
```text
  JWT Token String:
  [ Header ] . [ Payload ] . [ Signature ]
  (Base64)      (Base64)      (Hash Verification)

  HTTP Authorization Header:
  Authorization: Bearer <Token_String>
```
Explain the Signature: `Hash(Header + Payload + Server_Secret_Key)`. If the client alters the payload, the verification fails because the signature no longer matches.

### Teaching Flow
1. Explain the limitations of session databases in microservices.
2. Introduce JWT components: Header, Payload, Signature.
3. Show how the signature protects token integrity.
4. Explain Bearer Token transmission in HTTP headers.
5. Highlight the API testing workflow: Login -> Extract JWT -> Pass JWT in header.

### Live Coding Demonstration
#### Step 1
Open the public site `https://jwt.io` on your screen.
#### Step 2
Paste a sample JWT token (or use the default placeholder).
#### Step 3
Show the decoded Header, Payload (claims like username, role), and Signature fields.
#### Step 4
Manually edit a value in the Payload section (e.g. change `"role": "user"` to `"role": "admin"`).
#### Step 5
Show how the signature verification badge immediately turns red and says "Invalid Signature".

### Common Student Doubts
#### Student Question
"Is the data inside a JWT payload encrypted? Can someone read my roll number if they intercept the token?"
#### Trainer Response
"No! The header and payload are only Base64 *encoded*, not encrypted. Anyone can decode it using online tools. Therefore, you must never store sensitive information like passwords inside the JWT payload."

### Real World Discussion
In API automation testing, you fetch the token dynamically and configure the HTTP headers:
```javascript
const apiContext = await playwright.request.newContext({
  extraHTTPHeaders: {
    'Authorization': `Bearer ${token}`
  }
});
```

### Interview Talking Points
* **Interview Question:** "What are the three parts of a JWT token and where is it passed in request headers?"
* **Key Terminology:** *Bearer Token*, *Header-Payload-Signature*, *Stateless Authorization*.

### Topic Recap
* **Definition:** A JWT is a digitally signed data packet for secure, stateless verification.
* **Syntax:** `Authorization: Bearer <Token>`
* **Takeaway:** The signature detects client tampering; the payload must not contain plain credentials.

---

## Session Closing

### Session Summary
"Today we covered the fundamentals of the web:
1. Client-Server Architecture and how browsers request resources from server backends.
2. How the browser rendering engine paints HTML/CSS into the DOM tree, and how JS rendering affects timing.
3. How HTTP Status Codes notify us of successes or crashes.
4. The differences in browser storage: small cookies sent to the server, versus large Local/Session Storage.
5. How modern APIs secure requests statelessly using Bearer JWT tokens."

### Knowledge Check Questions
1. Which browser storage is wiped clean when the browser tab is closed? (Session Storage).
2. What status code is returned if the resource does not exist? (404 Not Found).
3. Can standard JavaScript read an `HttpOnly` cookie? (No).
4. What are the three parts of a JWT token? (Header, Payload, Signature).
5. Which HTTP method is typically used to send login credentials? (POST).

### Assignment Introduction
"To reinforce these concepts, please complete the Day 6 assignments in `02_Assignments.md`. These assignments test your ability to parse raw HTTP request logs, analyze cookie configurations, and build authorization header formats. These are exact mirrors of interview scenarios."

### Homework Guidance
* Try opening the network inspector tab while logging into your own college portal. Inspect the requests.
* Use `jwt.io` to inspect and understand any JWT tokens you find in your session storage.

### Next Session Preview
"In our next session, we will start writing automated scripts using Playwright. We will write scripts to open browsers, locate elements in the DOM tree, click buttons, and assert page title values."
