## Attack on GET Service

GET vs POST
There are two typical HTTP requests. One is a GET request and the other is POST request. The main difference here with regaurding CSRF attack is will the data be attached. 

<img width="340" height="65" alt="image" src="https://github.com/user-attachments/assets/d73dcc77-f97f-4fbb-a6b7-7b2a28f8b1fd" />
When you send the GET request, the data is attached in the URL. The URL is with the question mark and after that is the data that is usually value pair and seperated by the ampersands sign. 
Example: foo=hello&bar=world which has two arguments attached to the request.

<img width="297" height="121" alt="image" src="https://github.com/user-attachments/assets/22cc38ae-f61a-47d4-9c9c-0c5f86b2033b" />
For the POST request, the data is acuallty not attached in the URL. THe data is in the body section in the HTTP request.
POST request doesn't have the question mark. Then there's the header. Then the data attached to the POST request.

These differences make the attack different. 
For the GET request, because everything is attached to the URL, it is easier to launch the attack against the GET service.

<img width="445" height="208" alt="image" src="https://github.com/user-attachments/assets/425bb6cb-bcaf-4544-8444-db6fa9ba16c2" />

When HTML elements like img or iframe are placed inside a page, when the browser sees the image tag, it will immdiately initiate a GET request and the URL and the argument is specified by the page.

## The Add-Friend HTTP Request

We will host a website and the target website we are using is from open source project. They did a good job preventing this attack, but we will turn off the countermeasures in the source code. This will gave us the opportunity to launch this attack without contermeasures.

<img width="364" height="264" alt="image" src="https://github.com/user-attachments/assets/b0b554a5-2fae-44e8-a340-a4d79e912465" />


We have many users in this social network site. For the attack, our job is to add ourselves to a victim's friend list without their consent. To add friend list, Alice can add Boby which will trigger HTTP GET request. With all the parameters attached, if Alice can do that in front of her computer, what will prevent the attackers from doing that from Alice computer.
All we need is for Alice to vist this malicious Website. Once the code is on Alice machine, whatever that can be done manually, can be done automatically using the program. Now we can send the request, except our request is cross-site request versus the same-site request. Since the server won't tell the difference, the attack will be successful.
The key challenge here is to find out what request sends out when you click the button. We need to follow the same format, URL, and the agrument. We will inspect the request that sends out from our computer.

<img width="471" height="220" alt="image" src="https://github.com/user-attachments/assets/599e300a-3731-48fd-9708-cf46413a039e" />

We can use something called HTTP Header Live extension to capture the traffic that stands out from your machine. In the example above, the highlighted area is the URL. We also see the session ID that the browser attached for us. Without an session ID, you will not be able to add friends list because the server is going to know that you haven't been authenticated. Session ID is created after you made a username and password, and when you come back you bring back the token so you can be authenticated. 

## Forge GET Request 

<img width="396" height="256" alt="image" src="https://github.com/user-attachments/assets/d827b3d5-8a99-4525-9213-c02b94450ada" />

How do we forge our request? We need to create an inage attack and put the URL and argument in the source attribute. Now, we need to trick the user (victim) and come to this website. As soon as the click on it, this page will be brought to their computer. Once the browser sees the image tag, they will send out the request. The victim need to have an active session for this to work.


