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

<br>
<br>
<br>
<br>
<br>

# Demo: Attack

## Overview

This lab demonstrates a **Cross-Site Request Forgery (CSRF)** attack using the Elgg web application provided by SEED Labs. The objective was to understand how a malicious website can exploit an authenticated user's browser to perform unintended actions without the user's knowledge.

In this exercise, the attack causes a victim to automatically add another user as a friend simply by visiting the attacker's webpage.

---

## Learning Objectives

- Understand how Cross-Site Request Forgery (CSRF) attacks work.
- Learn how browsers automatically include session cookies in authenticated requests.
- Create a malicious webpage that performs a forged HTTP GET request.
- Observe how authenticated users can unknowingly execute actions.
- Understand common CSRF defenses used in modern web applications.

---

## Environment

- **Host OS:** Windows 11
- **Virtual Machine:** Ubuntu 20.04
- **Platform:** Docker (SEED Labs)
- **Browser:** Firefox
- **Target Application:** Elgg Social Network

---

## Attack Scenario

The victim logs into the Elgg web application.
<img width="691" height="395" alt="Screenshot 2026-07-23 212133" src="https://github.com/user-attachments/assets/2e3efff5-a1d7-4243-a8d9-89de6068e0b2" />


The attacker hosts a malicious webpage containing an image element that silently sends an HTTP GET request to Elgg.

Since the victim is already authenticated, the browser automatically includes the victim's session cookie.
<img width="684" height="277" alt="Screenshot 2026-07-23 212632" src="https://github.com/user-attachments/assets/8eec40c4-b93a-4639-becd-b428288ff3d7" />

Elgg processes the request as if the victim intentionally performed the action.

As a result, the attacker is added to the victim's friends list without the victim clicking any "Add Friend" button.
<img width="681" height="424" alt="Screenshot 2026-07-23 212707" src="https://github.com/user-attachments/assets/61f25d5c-32a5-4711-b8a8-c6f0118f3909" />


---

## Attack Implementation

The malicious webpage contains the following HTML:

```html
<!DOCTYPE html>
<html>
<body>
<h1>This page forges an HTTP GET request</h1>

<img src="http://www.seed-server.com/action/friends/add?friend=57"
     alt="image"
     width="1"
     height="1" />

</body>
</html>
```

The hidden image automatically sends a request to the target application when the page loads.

No JavaScript is required because the browser attempts to retrieve the image automatically.

---

## Results

Successful attack:

- Victim logged into Elgg.
- Victim visited the attacker's webpage.
- Browser automatically sent the authenticated request.
- Boby (User ID: 57) was added to the victim's friends list.

---

## Challenges Encountered

### 1. Incorrect Lab Environment

Initially downloaded the XSS lab instead of the CSRF lab.

**Resolution**

Downloaded the correct SEED CSRF Lab containing the attacker web server (`www.attacker32.com`).

---

### 2. Attacker Website Unreachable

The attacker website could not be accessed because the attacker container was not included in the Docker Compose configuration.

**Resolution**

Verified the Docker environment and launched the correct CSRF lab setup.

---

### 3. CSRF Attack Failed

The malicious HTML page loaded correctly, but the forged request redirected to the login page instead of adding the friend.

Using Firefox's Developer Tools (Network tab), I observed:

- HTTP GET request successfully sent
- HTTP Status 302
- Redirect to `/login`

The request included the Elgg session cookie, indicating the browser generated the request correctly.

**Root Cause**

Firefox's Enhanced Tracking Protection interfered with the lab's expected browser behavior.

**Resolution**

Disabled Enhanced Tracking Protection for the SEED Lab websites.

After disabling the protection, the browser successfully completed the authenticated request and the attack worked as expected.

<img width="1199" height="900" alt="Screenshot 2026-07-23 165317" src="https://github.com/user-attachments/assets/e6e5be44-0df2-4bb9-84da-bcf68bfcf08f" />


---

## Security Impact

Cross-Site Request Forgery allows attackers to abuse an authenticated user's browser.

Without proper protections, attackers can perform actions such as:

- Adding friends
- Changing account information
- Sending messages
- Updating passwords
- Performing financial transactions

All without the victim's knowledge.

---

## Defensive Measures

Modern web applications defend against CSRF attacks using:

- CSRF Tokens
- SameSite Cookie attributes
- Origin header validation
- Referer header validation
- User confirmation for sensitive actions

---

## Key Takeaways

- Browsers automatically include authentication cookies with requests.
- A victim does not need to click a button for a forged request to execute.
- Hidden HTML elements such as `<img>` can generate authenticated requests.
- Browser security features may affect security labs and require troubleshooting.
- CSRF tokens and SameSite cookies are effective defenses against this attack.

---

## Skills Demonstrated

- Web Application Security
- Cross-Site Request Forgery (CSRF)
- HTTP Requests
- Session Cookie Analysis
- Firefox Developer Tools
- Docker
- Linux
- Network Troubleshooting
- Security Testing
- Vulnerability Analysis
