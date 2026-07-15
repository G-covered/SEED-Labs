## Ajax and Its Security Model

<img width="320" height="301" alt="image" src="https://github.com/user-attachments/assets/b2f11e94-88dc-4a9e-a9a4-8957326309b0" />

To communicate with a backend server from the browser, we rely on three primary methods: **Standard HTTP Requests**, **Ajax**, and **WebSockets**.

### Standard HTTP vs. Ajax
* **Standard HTTP Request:** Before Ajax was introduced, updating any part of a webpage required a full page reload. For example, to update live stock prices, the browser had to send a standard HTTP request. When the server responded, the entire existing page was discarded, and a brand-new page was rendered from scratch. This led to a very jarring, inefficient user experience.
* **Ajax (Asynchronous JavaScript and XML):** Ajax sends HTTP requests in the background. The browser handles these requests silently. When the response arrives, the data is passed directly to the running JavaScript program *without* discarding the current page. The script can then dynamically manipulate the DOM to update only the specific elements that changed.

---

### How Ajax Works in Code

<img width="482" height="449" alt="image" src="https://github.com/user-attachments/assets/66ef3a6d-c201-4872-9838-17522032cf9b" />

Ajax operations are inherently **asynchronous**. Because JavaScript runs on a single main thread inside the browser, blocking that thread to wait for a network response would freeze the entire page, making it completely unresponsive to the user.

To prevent freezing, Ajax uses a **callback mechanism**:
1.  **Instantiate:** The script creates an `XMLHttpRequest` (or uses the modern `fetch` API).
2.  **Configure & Send:** You specify the HTTP method (e.g., `GET`), target URL, and trigger `.send()`.
3.  **Register Callback:** You assign a function to handle the response when it eventually arrives.
4.  **Execute:** When the server responds, the browser triggers your callback function, letting you use properties like `responseText` and DOM APIs (such as `innerHTML`) to update the page.

---

## The Same-Origin Policy (SOP) on Ajax

Standard HTTP requests and Ajax requests have vastly different security implications.

* **Standard HTTP Security:** If you are visiting a malicious site (Site A) and click a link that fires a standard HTTP request to your bank (Site B), the bank's response is rendered as a brand-new page. Site A's JavaScript has *no way* to read the HTML or session data of this new page because they are completely isolated.
* **The Ajax Vulnerability:** Because Ajax returns data directly to the executing JavaScript engine on the current page, we need strong protections. Without restrictions, if you visited malicious Site A while logged into your bank in another tab, Site A's background JavaScript could silently trigger an Ajax request to the bank and read your private balance data directly.

To prevent this cross-site data theft, browsers enforce the **Same-Origin Policy (SOP)**.

### What is the Same-Origin Policy?
By default, JavaScript can only read the response of an Ajax request if the request's target origin (protocol, domain, and port) matches the origin of the page running the script.
* If a page loaded from `https://google.com` attempts an Ajax request to `https://facebook.com`, the browser's sandbox will block the Google script from reading the returned data, keeping your Facebook session safe.

---

## Cross-Origin Resource Sharing (CORS)

Often, web developers *want* different origins to share data securely (for example, displaying a third-party weather widget). In these cases, the destination server must explicitly authorize the cross-origin request.

<img width="514" height="329" alt="image" src="https://github.com/user-attachments/assets/986eb046-a81f-41f9-b6f1-3eb4a720db47" />

*Above: A standard Cross-Origin request being blocked by the browser because the target server did not authorize it.*

<img width="420" height="120" alt="image" src="https://github.com/user-attachments/assets/760d8b30-750f-4276-9796-ba2d4d7a7f06" />

*Above: When the target server includes proper CORS headers, the browser allows the calling site to read the response.*

By attaching specific headers in its HTTP response (such as `Access-Control-Allow-Origin: https://trustedsite.com`), the destination server tells the browser sandbox that it is safe to lift the SOP restriction for that specific caller.

---

## Bypassing SOP: DNS Rebinding Attacks

<img width="386" height="250" alt="image" src="https://github.com/user-attachments/assets/6d4376b8-75db-42a3-9d49-27a16ca4ca50" />

Because the browser's Same-Origin Policy relies on the **domain name** (rather than the raw IP address) to determine origin, attackers can exploit the Domain Name System (DNS) to bypass SOP. This is known as a **DNS Rebinding Attack**.

### Scenario: Attacking Smart IoT Devices
Many smart IoT devices (like routers or thermostats) run local web servers behind a home firewall.
* These local web interfaces can only be accessed from within the local home network.
* Many IoT devices lack strong authentication because they assume the home firewall is enough protection.
* The browser's SOP naturally stops external websites from using Ajax to read or control these local devices.

### How DNS Rebinding Bypasses SOP:
1.  **The Bait:** You visit the attacker’s malicious page (e.g., `http://attacker.com`). Your browser resolves `attacker.com` to the attacker’s malicious server IP (e.g., `203.0.113.5`) and loads the attacker's web page containing malicious Ajax code.
2.  **The Rebind:** The attacker sets a very short **Time-to-Live (TTL)** on their DNS record so your browser's DNS cache expires instantly. The attacker then changes their DNS server mapping: `attacker.com` now points to the local IP of your smart thermostat (e.g., `192.168.1.100`).
3.  **The Request:** The malicious Ajax script on your page sends a new request to `http://attacker.com/set-temp`. 
4.  **The Bypass:** Because the domain name is still `attacker.com`, the browser believes this request complies with the **Same-Origin Policy** and allows the request to proceed. 
5.  **The Attack:** Behind the scenes, the browser resolves the expired domain to its new IP address (`192.168.1.100`). The request is sent directly to your local IoT thermostat, allowing the malicious script to read its status or change its settings.

---

## WebSockets



While Ajax allows dynamic updates, it is fundamentally a **client-pull** mechanism. The server cannot push data to the client; the client must always initiate the connection. This limitation led to the creation of **WebSockets**.

* **Behavior:** A WebSocket initiates as a standard HTTP handshake, but then upgrades to a persistent, full-duplex TCP socket connection. This allows the server to instantly push real-time updates directly to the client without waiting for a request.

### WebSocket Security Considerations (Critical Difference)
The crucial security difference between Ajax and WebSockets lies in how they handle origin policies:
* **Ajax:** The browser **automatically** enforces the Same-Origin Policy on the client-side.
* **WebSockets:** The browser **does not** enforce the Same-Origin Policy. It allows cross-origin WebSocket connections by default. 

> **Developer Responsibility:** Because the browser's sandbox does not block cross-origin WebSockets, security must be implemented on the **server-side**. The backend server must explicitly inspect the incoming `Origin` header during the initial handshake and decide whether to accept or reject the connection.
