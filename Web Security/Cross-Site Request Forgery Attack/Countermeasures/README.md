## Fundamental Causes

Before we get to countermeasures, we need to understand the fundamental cause of this attack. 
The cross-site request forgery occurs when an attacker causes a victim's browser to send an unwanted authenticated request directly to a target server, making the server believe the request came from the legitimate user. Where the request come from are different, but the information attached to the request are the same like cookies and session ID. The server would not be able to tell the difference if the request comes from my page or someone else's page. 
Would the browser know the difference between across-site request or not? The browser would know because it knows which page initiated the request as well as the target.
The problem here is that the browser cannot convey that information to the server. 
The browser can definitely protect you against that. But the protections should be on the server side, not on the browser side. That is a missing link between the browser and the server.
There is a piece of information that actually the browser does send to the server which is called a referer header. So, this lets the server know where the request came from.
Because the referer header discloses too much information about the user's behavior, many users block this field. Although this is helpful, it's unreliable.
What would be something less revealing but still help the server to know whether this is a cross-site request or not?

So, what can we do that the other side cannot do? If you can leverage that, you can help the server to identify whether it's you or someone else.
