# Attacking POST Services

Unlike GET requests, **POST** requests store their data in the **HTTP request body** instead of the URL. Because the request parameters are not visible in the URL, forging a POST request is slightly more difficult than forging a GET request.

In this lab, the objective is to modify another user's profile. Normally, a user edits their own profile through the web interface, and clicking **Save** sends an HTTP POST request to the server.

A CSRF attack attempts to replicate this same process by automatically filling out and submitting a form from a malicious webpage.

---

# The Edit-Profile POST Request

To successfully forge a POST request, we first need to determine exactly what the legitimate request looks like.

This can be done using browser developer tools or extensions such as **HTTP Header Live**, which capture every HTTP request sent by the browser.

An example request is shown below.

## Request URL

```text
http://www.seed-server.com/action/profile/edit
```

Unlike a GET request, notice that **no data appears in the URL**.

---

## HTTP Headers

The browser automatically includes several HTTP headers with the request, such as:

```text
Host: www.seed-server.com
User-Agent: Mozilla/5.0 ...
Accept: text/html,...
Content-Type: multipart/form-data
Content-Length: ...
Origin: http://www.seed-server.com
Referer: http://www.seed-server.com/profile/charlie/edit
Cookie: Elgg=<session-id>
```

The most important header is the **Cookie** header.

The browser automatically attaches the authenticated session cookie, allowing the server to recognize the logged-in user. The attacker does not need to know the session ID because the victim's browser includes it automatically.

---

## POST Data

The actual profile information is stored inside the request body.

A simplified example is shown below:

```text
__elgg_token=...
__elgg_ts=...
name=Charlie
briefdescription=Samy is my hero!
accesslevel[briefdescription]=2
description=...
guid=58
```

Some important fields include:

| Parameter | Purpose |
|-----------|---------|
| `__elgg_token` | CSRF protection token (disabled during this lab) |
| `__elgg_ts` | Timestamp used with the CSRF token |
| `name` | User's display name |
| `briefdescription` | Profile description |
| `accesslevel[...]` | Controls who can view the profile field |
| `guid` | Unique identifier of the target user |

The `guid` parameter is especially important because it identifies which user's profile is being modified.

---

# Sending POST Requests

One of the easiest ways to generate a POST request is through an HTML form.

For example:

```html
<form action="http://www.example.com/action.php" method="post">

  <label>First Name:</label>
  <input type="text" name="fname">

  <label>Last Name:</label>
  <input type="text" name="lname">

  <input type="submit" value="Submit">

</form>
```

When the user submits the form:

1. The browser collects the values entered into the input fields.
2. It places those values inside the HTTP request body.
3. A POST request is sent to the URL specified in the `action` attribute.

This is the normal mechanism used by web applications when users submit forms.

---

# Forging POST Requests

A CSRF attack recreates this same form submission process without the victim's knowledge.

Instead of displaying a visible form, the attacker:

1. Dynamically creates a hidden HTML form.
2. Fills the form with the desired values.
3. Adds the form to the webpage.
4. Automatically submits the form when the page loads.

A simplified example is shown below:

```javascript
function send_post() {

    var fields = "";
    fields += "<input type='hidden' name='name' value='Bob Smith'>";
    fields += "<input type='hidden' name='age' value='20'>";

    var form = document.createElement("form");

    form.action = "http://www.example.com";
    form.method = "post";
    form.innerHTML = fields;

    document.body.appendChild(form);

    form.submit();
}

window.onload = function () {
    send_post();
};
```

### How It Works

The script performs the following steps:

1. Creates hidden input fields containing the attacker's chosen values.
2. Dynamatically creates an HTML form.
3. Sets the form's destination (`action`) to the target website.
4. Specifies that the form should use the **POST** method.
5. Appends the form to the webpage.
6. Automatically submits the form as soon as the page loads.

From the victim's perspective, nothing appears unusual because the form is hidden.

If the victim is already logged into the target website, the browser automatically attaches the authenticated session cookie when submitting the POST request.

As a result, the target server processes the request as though it were submitted directly by the victim, allowing the attacker to modify the victim's profile without their knowledge.

<br>
<br>
<br>
<br>

# CSRF Attack - HTTP POST (Modify Profile)

## Overview

This portion of the SEED CSRF Lab demonstrates how an attacker can forge an **HTTP POST** request to modify an authenticated user's profile without their knowledge.

Unlike the previous GET request attack, this attack recreates the legitimate profile update request using a hidden HTML form that automatically submits itself when the victim visits the attacker's webpage.

---

# Objective

Modify the victim's profile information by creating a malicious webpage that automatically submits an HTTP POST request to the Elgg server.

---

# Capturing the Legitimate Request

Before constructing the attack, I first captured the legitimate profile update request.

Using **Firefox Developer Tools (Network tab)**, I edited my profile and clicked **Save** while monitoring the generated HTTP requests.

The captured request showed:

**Request Method**

```text
POST
```

**Request URL**

```text
http://www.seed-server.com/action/profile/edit
```
<img width="754" height="174" alt="Screenshot 2026-07-31 133333" src="https://github.com/user-attachments/assets/59658994-6161-485a-8104-389f80a3f66a" />

<br>
<br>

The request contained several form parameters, including:

```text
name
description
briefdescription
location
interests
skills
contactemail
phone
mobile
website
twitter
guid
```

It also included the following CSRF protection parameters:

```text
__elgg_token
__elgg_ts
```

For this portion of the lab, these parameters were intentionally ignored as instructed because they are part of Elgg's CSRF defense mechanism.

---

# Attack Construction

The attacker webpage used JavaScript to dynamically create a hidden HTML form.

Hidden input fields were added to the form containing the profile information that would be submitted to the server.

Once the page loaded, the form automatically submitted itself without requiring any interaction from the victim.

The form was submitted to:

```text
http://www.seed-server.com/action/profile/edit
```

using the HTTP POST method.

---

# Attack Flow

```text
Victim logs into Elgg
          │
          ▼
Victim visits attacker webpage
          │
          ▼
JavaScript creates a hidden HTML form
          │
          ▼
Hidden profile fields are inserted
          │
          ▼
Form automatically submits
          │
          ▼
Elgg processes the POST request
          │
          ▼
Victim's profile is modified
```

---

# Results
### Before visiting the attacker's webpage:
<img width="387" height="307" alt="image" src="https://github.com/user-attachments/assets/67636f4e-c16f-4c25-8344-ed7de81f04f3" />

### Aftermath
<img width="435" height="306" alt="image" src="https://github.com/user-attachments/assets/adec596a-251a-406e-be73-dffe4c44d4ad" />

After visiting the attacker's webpage while logged into Elgg:

- The hidden form was automatically submitted.
- The forged POST request was accepted by the server.
- The victim's profile information was successfully modified without manually editing the profile.

---

# Challenges Encountered

## Capturing the POST Request

Unlike the GET attack, constructing the POST request required identifying all of the form fields submitted by Elgg.

### Resolution

Used Firefox Developer Tools to inspect the legitimate POST request generated when updating a profile and recreated the required form fields.

---

## Browser Cache

While testing the attack, Firefox repeatedly loaded an outdated version of `editprofile.html`, causing the browser to execute old JavaScript code.

### Resolution

Opened the page using a Private Browsing window to verify the updated file.

After confirming the issue was caused by browser caching, I disabled the browser cache while developing and cleared browser cookies to ensure the latest version of the attacker page was loaded.

---

# Security Impact

If an application does not properly defend against CSRF attacks, an attacker can cause authenticated users to unknowingly modify their own account information simply by visiting a malicious webpage.

Potential consequences include:

- Modifying profile information
- Changing account settings
- Updating contact information
- Performing unauthorized actions while authenticated

---

# Defensive Measures

Modern web applications commonly defend against CSRF attacks by implementing:

- CSRF Tokens
- SameSite Cookie attributes
- Origin header validation
- Referer header validation
- User confirmation for sensitive operations

---

# Key Takeaways

- HTTP POST requests can be forged just as easily as GET requests when proper protections are absent.
- Firefox Developer Tools are valuable for capturing and analyzing legitimate HTTP requests.
- Browser caching can interfere with testing web security attacks.
- CSRF tokens provide an effective defense by preventing forged requests from being accepted.
- Understanding how legitimate requests are structured is essential when analyzing or testing web application security.

---

# Skills Demonstrated

- Cross-Site Request Forgery (CSRF)
- HTTP POST Requests
- Web Application Security
- Firefox Developer Tools
- Session Analysis
- JavaScript
- HTML Forms
- Docker
- Linux
- Security Testing
- Vulnerability Analysis
