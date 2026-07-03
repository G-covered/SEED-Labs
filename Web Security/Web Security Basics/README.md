## Basic Web Architecture

<img width="425" height="195" alt="Screenshot 2026-06-24 212536" src="https://github.com/user-attachments/assets/d617d07a-9f4d-48ab-b381-f6d76c5cebd8" />

A web browser communicates with a web server to request and receive web pages and other resources.

The browser and web server communicate using the **HTTP (Hypertext Transfer Protocol)**.

- **HTTP** is a protocol used to transfer web content between a client and a server.
- **HTTPS (Hypertext Transfer Protocol Secure)** is the encrypted version of HTTP, which protects data in transit using TLS/SSL.

If the web application uses a database, the web server communicates with the database using **SQL (Structured Query Language)** queries.

### Communication Flow

Browser → HTTP/HTTPS → Web Server → SQL → Database

The typical process is:

1. The browser sends a request to the web server.
2. The web server processes the request.
3. If data is needed, the server queries the database using SQL.
4. The database returns the requested data to the server.
5. The server sends a response back to the browser.

<br>
<br>

## Web Browser

### Content vs. Presentation
*   **HTML:** Describes the structure and core content of a web page.
*   **CSS:** Focuses entirely on the presentation and styling (e.g., layouts, colors, and fonts).

### Browser Components: Plug-ins vs. Extensions
To provide features that native browsers couldn't initially support, two separate add-on models were created:

*   **Plug-ins (Binary Code)**
    *   Executed as compiled **binary code** (for example, legacy PDF viewers).
    *   **Security Impact:** Because they run as native binaries, they exist "inside the gate." They are not a sandboxed part of the browser and have direct access to system memory. This makes isolating them incredibly difficult, turning legacy plug-ins into a major source of browser vulnerabilities.
*   **Extensions (API-Driven)**
    *   Interact with the browser by requesting information and actions through a restricted **browser API**.
    *   **Security Impact:** Extensions do not have arbitrary memory access or access to protected system gates. They must operate strictly within the permissions granted by the browser's security framework.


### Dynamic Content

Historically, dynamic content was introduced to allow platforms to animate and add interactivity to websites. 

### Legacy Platforms (Deprecated)
*   Adobe Flash
*   Microsoft Silverlight
*   ActiveX
*   Java applets

> ⚠️ **Security Takeaway:** These platforms relied heavily on the risky binary plug-in model, introducing massive security holes. Thanks to modern, native browser capabilities, these legacy third-party platforms are no longer necessary and have been completely phased out.

### The Modern Standard: JavaScript
*   **JavaScript** is now the universally accepted standard for creating dynamic content.
*   True interactive features are now handled natively by the browser using **HTML5** and JavaScript, completely eliminating the need for insecure external software.
