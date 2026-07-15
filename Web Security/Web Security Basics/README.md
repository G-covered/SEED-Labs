# 🌐 Web Security Basics: Core Architectures & Sandboxing

This repository contains structured notes exploring how modern web applications function and the security boundaries designed to protect users, browsers, and backend servers. Below is an executive summary of the foundational concepts documented in this repository.

---

## 1. Browser Architecture & The Sandbox
The modern web browser is designed to execute untrusted code (JavaScript) safely. 
* **Content vs. Presentation:** HTML defines structure; CSS handles styling.
* **Plug-ins vs. Extensions:** Legacy binary plug-ins (e.g., Flash, Java Applets) ran with native privileges and direct memory access, creating massive security vulnerabilities. Modern extensions are restricted to browser APIs and operate within a managed permission model.
* **The Sandbox:** Browsers run JavaScript inside a sandboxed engine (like V8 or SpiderMonkey) which interprets code into bytecode and audits actions before they hit the CPU. This prevents untrusted web scripts from arbitrarily accessing the local filesystem, private browser configurations, or other open tabs.

---

## 2. Server Architecture & State Management
Web servers process requests, but because the underlying protocol is stateless, they rely on specialized state-tracking mechanisms.
* **Static vs. Dynamic:** HTTP servers (like Nginx) resolve static files, while Web Application servers execute the dynamic logic (e.g., PHP, Node.js).
* **The Stateless Problem:** Every HTTP request is independent and handled by an isolated server thread. The server does not naturally remember a user from one request to the next.
* **Cookies & Sessions:** To maintain state (like login status or shopping carts), servers issue a unique, high-entropy **Session ID** cookie to the client. The browser automatically attaches this cookie to future requests, allowing the server to look up the user's secure state in its backend database. Protecting this Session ID is a primary goal of web security to prevent **Session Hijacking**.

---

## 3. Asynchronous Communication & Security Policies
Modern web applications rely on background, asynchronous communication, requiring robust security constraints.
* **Ajax vs. Standard HTTP:** Standard HTTP requests reload the entire page, isolating new data. Ajax (`fetch` / `XMLHttpRequest`) delivers backend data directly back to the active page's JavaScript environment, requiring strict access control.
* **Same-Origin Policy (SOP):** The browser's primary client-side shield. It prevents scripts on one website (e.g., a malicious page) from reading sensitive response data from another origin (e.g., your bank) unless explicitly authorized by **CORS (Cross-Origin Resource Sharing)** headers.
* **DNS Rebinding:** An advanced bypass where an attacker exploits short DNS TTLs to rebind their domain name to a victim's local network IP (like an IoT device behind a home firewall). This tricks the browser into thinking the request complies with SOP.
* **WebSockets:** Unlike Ajax, WebSockets establish persistent, full-duplex TCP connections. Critically, **the browser does not enforce SOP on WebSockets**—origin validation must be handled on the server side during the handshake.

***

###  How to Use This Repo
Navigate through the folders in this repository to read in-depth technical breakdowns, visual diagrams, and core security code snippets for each of these areas!
