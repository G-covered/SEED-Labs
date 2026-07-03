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
