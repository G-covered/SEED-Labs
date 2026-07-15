## Web Server Architecture

<img width="426" height="303" alt="image" src="https://github.com/user-attachments/assets/4cca4635-340f-4e46-a83d-171b40bc7de7" />

When a web browser communicates with a server via HTTP, the backend typically splits the workload between two distinct components:

1.  **HTTP Web Server:** A generic entity (like Apache or Nginx) tasked with handling incoming raw HTTP requests. 
    * **Static Content:** If a client requests a static file (like an image or a plain HTML file), the HTTP server handles it directly by loading the file from disk and returning it to the client.
2.  **Web Application Server:** A specialized program containing the core business logic of the website.
    * **Dynamic Content:** If the request requires dynamic page generation (e.g., pulling data from a database), the HTTP server doesn't know how to handle it. It hands the request off to the web application layer to compute and generate the response.

### The Evolution of Backend Communication
* **Legacy (CGI):** Historically, the HTTP server and the web application communicated using a standard interface called **CGI (Common Gateway Interface)**. For example, if a `.php` file was requested, the HTTP server spawned a separate process to run the PHP script. Creating separate processes for every request introduced heavy performance overhead.
* **Modern Modules:** To solve this overhead, modern web architectures embed the application runtimes directly into the HTTP server as highly optimized **modules** (or use persistent fast-process managers like PHP-FPM).

---

## Interacting with the Server

Client-side JavaScript code interacts with the backend server scripts using three primary communication channels, all of which rely fundamentally on the **HTTP/TCP protocol suite**:

### 1. Standard HTTP Requests
* Triggered by standard browser actions (like clicking a link or submitting a basic HTML form).
* **Behavior:** The data returned by the server is **not** handed back to the executing JavaScript. Instead, the browser intercepts the response and uses it to render an entirely new page. Either replacing the current page in the **same tab** or opening it in a **separate tab**.

### 2. AJAX Requests (Asynchronous JavaScript and XML)
* Triggered programmatically via APIs like `fetch()` or `XMLHttpRequest`.
* **Behavior:** The data fetched from the server goes **directly back to the JavaScript code** that requested it, allowing the page to update asynchronously without a reload. The browser's built-in sandbox evaluates the relevant security policies (such as CORS) before deciding whether the script is allowed to read the returned data.

### 3. WebSockets
* Establishes a persistent, full-duplex communication channel over a single TCP connection.
* **Behavior:** While it initiates via an HTTP handshake, it allows real-time, bi-directional data streaming. Like AJAX, the data flows directly back into the JavaScript runtime, though it handles connections differently than standard stateless HTTP requests.

---

## HTTP Request and Response Lifecycle

<img width="405" height="342" alt="image" src="https://github.com/user-attachments/assets/5fd855b3-a341-4947-85cf-36fe9b56d9de" />

### The HTTP Request
An incoming request specifies the HTTP method/type (e.g., `GET` or `POST`), followed by:
* **HTTP Headers:** Standardized metadata used by the server to understand the client's context (e.g., User-Agent, Accept-Language, Cookies).
* **Request Body:** The actual data payload sent to the server (primarily used in `POST` requests).

### The HTTP Response
The backend returns data payload alongside critical protocol metadata:
* **HTTP Status Code:** Indicates the result of the request (e.g., `200 OK`, `404 Not Found`).
* **Response Headers:** Metadata sent back to the browser. 

>  **Security Impact:** Response headers are highly critical for web security. They allow the server to explicitly dictate security instructions to the browser sandbox (e.g., enforcing `Content-Security-Policy`, `X-Frame-Options`, or cookie security attributes).

---

## GET vs. POST Requests

<img width="339" height="276" alt="image" src="https://github.com/user-attachments/assets/73d98f11-5041-41ec-bfc7-7121a5c187ac" />

The key functional and structural difference between these two primary request methods lies in how they transmit data:

| Request Type | Data Location | Description |
| :--- | :--- | :--- |
| **GET** | Appended to the URL | Appends variables directly to the URL path as a query string (e.g., `/search?q=security`). Because data is exposed in the address bar, browser history, and server logs, it should never be used for sensitive parameters. |
| **POST** | Enclosed in the Request Body | Encapsulates the payload inside the data body rather than the URL. This isolates the data from plain sight in the browser URL bar, making it the standard choice for submitting forms, credentials, and file uploads. |
