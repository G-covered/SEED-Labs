## The Stateless Nature of the Web

To understand why cookies exist, it is essential to look at how the web fundamentally operates. Unlike traditional client-server protocols (such as Telnet or SSH) where a connection remains open and active for the entire duration of a session, the Web is fundamentally **stateless**.

### How Web Connections Work
Every time a browser sends an HTTP request, the web server assigns a process or thread to handle it. Once the server returns the response, the connection is instantly terminated, and that process or thread goes back into an idle pool. 

When you click another link a second later, a completely different process or thread might handle your request. Because these server processes do not share an internal memory state with one another, the server has no native memory of your previous interactions.

>  **The Customer Service Analogy:** Imagine dialing a customer support center and spending 15 minutes explaining a complex issue to an agent. If the call suddenly drops and you dial back, you will almost certainly connect with a completely different agent. You have to repeat your entire story from the beginning because the new agent has no idea you just spoke to someone else. 

In online shopping, a transaction requires multiple distinct requests (browsing items, adding a product to a cart, and checking out). Because the web is stateless, a server would naturally treat the checkout request as a complete stranger, losing track of your cart entirely. **Cookies** were introduced to solve this exact problem.

---

## Cookies

Because web browsing is unpredictable—a user might make their next request in one second or one day—servers cannot afford to keep dedicated processes open for single users. To scale and serve millions of users simultaneously, the server offloads the burden of maintaining state to the client.

Essentially, the server tells the browser: *"I am not going to remember who you are, so I am handing this piece of data to you to hold onto."* This data is a **cookie**. The browser stores it locally and automatically attaches it to every subsequent request sent back to that same server, allowing the backend to reconstruct the user's session state.

---

## Setting Cookies

<img width="429" height="485" alt="image" src="https://github.com/user-attachments/assets/b8129e22-764c-4dd1-8325-84b78c57b826" />

---

## Web Tracking Using 3rd-Party Cookies

<img width="399" height="274" alt="image" src="https://github.com/user-attachments/assets/2c557f56-9204-4625-9ee7-f8e3cbdcc672" />

While cookies were designed to maintain user sessions, they are also heavily utilized for web tracking and targeted advertising. Ad networks can build highly descriptive behavioral profiles of users across completely unrelated websites.



### How 3rd-Party Tracking Works:
1. **Site A:** You visit an online store. Embedded inside the page is a tiny, often invisible tracking pixel or image hosted by a third-party advertising network.
2. **The Initial Request:** Your browser sends a request to the ad network's server to fetch that image. Because it is your first time interacting with this ad network, you do not have their cookie. The ad network's server responds by setting a unique **tracking token (cookie)** in your browser.
3. **Site B:** Later, you visit a completely unrelated blog. If this blog hosts an image or script from the *same* ad network, your browser will fetch it—automatically attaching the tracking cookie set during your visit to Site A.
4. **Profiling:** The ad network reads the incoming token, notes that the same unique browser has visited both Site A and Site B, and begins mapping your interests.

**Mitigation:** Users can disrupt this tracking by routinely clearing cookies, utilizing private browsing modes, or installing tracking-blocker extensions.

---

## Sessions and Session Cookies

Cookies are stored on the client side, meaning they are completely within the user's control. Because a user can alter or manipulate local cookie values, storing sensitive information (like user privileges or shopping cart totals) directly inside a standard cookie is a major security risk.

To solve this, applications use **Sessions**:

* **Session ID:** Instead of storing raw data on the client, the server generates a unique, random string called a **Session ID** and sends it to the browser as a cookie.
* **Server-Side Storage:** The actual sensitive data (cart items, account details) is safely stored on the server's backend, indexed entirely by that specific Session ID.
* **The Handshake:** When the browser makes a new request, it passes the Session ID cookie back. The server looks up the ID in its database and instantly restores the correct user state.

### Security Implications
The Session ID frequently serves as an active **Authentication Token**. Once a user successfully logs in, the server issues a Session ID to remember that the user is authenticated so they do not have to re-enter their password on every single click.

>  **Critical Security Risk:** Because a Session ID acts as proof of an active login, it is highly sensitive. 
> * **Session Hijacking:** Historically, when web traffic was unencrypted (`http://`), attackers sitting on public Wi-Fi networks could use packet sniffers to steal passing Session IDs out of the air. Once cloned, an attacker could inject that ID into their own browser and completely impersonate the victim's account without ever knowing their password.
> * **Modern Context:** Securing Session IDs from exposure is the fundamental defense requirement against both **Cross-Site Scripting (XSS)** and **Cross-Site Request Forgery (CSRF)** attacks.
