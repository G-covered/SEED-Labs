# Attacking GET Services

## GET vs. POST Requests

HTTP primarily uses two request methods:

- **GET**
- **POST**

From a CSRF perspective, the biggest difference is **where the request data is stored**.

### GET Requests

![GET Request Format](https://github.com/user-attachments/assets/d73dcc77-f97f-4fbb-a6b7-7b2a28f8b1fd)

With a **GET** request, the request parameters are included directly in the URL after a question mark (`?`).

For example:

```text
http://example.com/page?foo=hello&bar=world
```

Here:

- `foo=hello`
- `bar=world`

are URL parameters separated by the ampersand (`&`) character.

Because all of the data is visible in the URL, GET requests are generally easier to exploit with CSRF attacks.

### POST Requests

![POST Request Format](https://github.com/user-attachments/assets/22cc38ae-f61a-47d4-9c9c-0c5f86b2033b)

With a **POST** request, the request parameters are **not** included in the URL.

Instead, an HTTP POST request consists of:

- The request URL
- HTTP headers
- A message body containing the submitted data

Since the parameters are stored in the body of the request rather than the URL, forging POST requests typically requires additional techniques compared to GET requests.

---

## Why GET Requests Are Easier to Exploit

Because a GET request contains all of its data inside the URL, attackers can trigger one simply by embedding the URL inside common HTML elements.

![GET-Based CSRF Attack](https://github.com/user-attachments/assets/425bb6cb-bcaf-4544-8444-db6fa9ba16c2)

For example, elements such as:

- `<img>`
- `<iframe>`

automatically cause the browser to issue a GET request when the page loads.

If the `src` attribute points to a vulnerable URL containing the required parameters, the browser immediately sends the request without requiring any user interaction.

---

# The Add-Friend HTTP Request

The target application in this lab is an open-source social networking website.

Although the application normally includes protections against CSRF attacks, those defenses have been intentionally disabled so the attack can be demonstrated.

![Add Friend Request](https://github.com/user-attachments/assets/b0b554a5-2fae-44e8-a340-a4d79e912465)

The goal of the attack is to add the attacker to a victim's friend list without the victim's knowledge or consent.

Under normal circumstances:

1. Alice logs into the social network.
2. Alice clicks **Add Friend** on Bob's profile.
3. The browser sends an HTTP GET request containing the required parameters.
4. The server processes the request and adds Bob as Alice's friend.

A CSRF attack simply reproduces this same request.

If an attacker can cause Alice's browser to send the identical HTTP request, the server cannot distinguish it from a legitimate request because the browser automatically includes Alice's authenticated session cookie.

The only challenge for the attacker is determining exactly what HTTP request the application sends.

---

## Capturing the HTTP Request

![Captured HTTP Request](https://github.com/user-attachments/assets/599e300a-3731-48fd-9708-cf46413a039e)

To reproduce the request, we first inspect the network traffic generated when clicking the **Add Friend** button.

This can be done using browser developer tools or browser extensions such as **HTTP Header Live**.

The captured request reveals:

- The target URL
- All required URL parameters
- The HTTP method (GET)
- The session cookie automatically attached by the browser

The session cookie is especially important because it proves the user has already authenticated.

The attacker never needs to know or steal the session ID. As long as the victim is logged in, the browser automatically includes it in every request sent to the target website.

---

# Forging the GET Request

![Forged GET Request](https://github.com/user-attachments/assets/d827b3d5-8a99-4525-9213-c02b94450ada)

Once the attacker knows the correct URL and parameters, creating the CSRF attack is straightforward.

The malicious webpage simply embeds the forged URL inside an HTML element such as an image:

```html
<img src="http://target-website.com/action?parameter=value">
```

When the victim visits the malicious webpage:

1. The browser loads the page.
2. The browser encounters the `<img>` element.
3. It automatically sends the GET request specified in the `src` attribute.
4. The victim's authenticated session cookie is attached automatically.
5. The target server processes the request as though it came directly from the victim.

For the attack to succeed, the victim must already have an active authenticated session with the target website.
