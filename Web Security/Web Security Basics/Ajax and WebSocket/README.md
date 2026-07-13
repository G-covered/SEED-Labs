## Ajax and Its Security Policy

<img width="320" height="301" alt="image" src="https://github.com/user-attachments/assets/b2f11e94-88dc-4a9e-a9a4-8957326309b0" />

There are three ways to communicate with the server: normal HTTP request, Ajak, and WebSocket.
What is the difference between Ajax and normal HTTP request?
Before Ajax was introduced, it was not easy to update something inside the page using the data from the server.
Example: The quote of stock exchanges always changes every second. To update it, you would need to send a normal HTTP request and a new page will come back with the same information but with the updated part. The new page will come in, the old page will be kicked out, and the new page will be displayed. That's how you saw the contents changed. Making this inefficient and uneven transition.
Ajax changed that and basically does the same thing. To update something, you would send out a Ajax request. The inside is still HTTP, but it's a Ajax mechanic which the browser knows the different. When the results come in, the data will be given to this same page (not as a new page). Then the data will come in and give to the program to run in the page. So that program can update the content. So in short, the page is talking to the server, get the new data, and change the display inside the page. This is how Ajax fundamentally changed how this works, making is more dynamic.

<img width="482" height="449" alt="image" src="https://github.com/user-attachments/assets/66ef3a6d-c201-4872-9838-17522032cf9b" />

How do you write Ajax? It is acuallty not so complicated.
It send an Ajax request, create XMLHttpRequest(object). Once you have the object, you can send the request. Ajax is asynchronous. Meaning it will stay in the same page. When you send a request, you don't want to freeze this page because it's in one process. If you block it, you need to wait for response which takes a while and the user cannont click anything.
So if you file a request, then it will just continue on. THen when the result comes back, the browxer will notify if you have a registered a callback function.
So you need to register function() which is a callback function. When the response comes in, it calls the function. Inside the function, you get the response data.
innerHTML = this.responseText will fill in the placeholder for data.
You can use innerHTML to find that ID used in HTML to change its content. This is how you get that data and how you respond to that data.
Ajax still sends HTTP request so need you specify the type of request (e.g. GET), the URL, and send().

## Same Origin Policy on Ajax
Ajak and HTTP request are different security consequences. When you send a normal HTTP request, a new page will come back. 

The data is going to give it to the new page, and never to the old page.
For example: If the page comes from Google, inside of that you will send a request to Facebook. The result will come from Facebook, but will not give the data to the Google page. The result will become a new page that will be displayed. It will overwritting the Google page in the same tab or in a different tab or in a new window.

Ajax is fundamentally different Ajax. You send a request and the data needs to come back and give it back. We didn't put any protection for this kind of communication. Now, Google page can ask that data from Facebook from your account, because you have an active session in both Google and Facebook, Google can fetch data from Facebook. This a problem because you don't want data from one thing to jump to another automatically without authorization. So, the security policy is put on top of Ajax to restrict what data can be given to the page. You can still trigger the request and there's no restriction against triggering. Whether the servers can respond or not will depend on the server. Sometimes the server may not respond because it donesn't come from their page. If responded, the data that you will receive will be guarded by security policy.

What is the security policy? This is actually the policy for many things on the Web.
If I come from same origin, same place, same server, you have all the privilage. If you don't come from the same origin, for example, site A and site B are not the same site. The page comes from A and trying to get site B's data is against some origin policy. By default, the Ajax request will not be able to get the response. The browser will prevent you from getting a response. This provide protection to prevent site A to get the data from site B.

## Cross-Origin Ajax Request

Somethimes you want data to go from site B to site A, which there is demand for that.
But site B has to explicitly say that I do allow site A to take my data.

<img width="514" height="329" alt="image" src="https://github.com/user-attachments/assets/986eb046-a81f-41f9-b6f1-3eb4a720db47" />
In this image you see the Cross-Origin request is blocked.

<img width="420" height="120" alt="image" src="https://github.com/user-attachments/assets/760d8b30-750f-4276-9796-ba2d4d7a7f06" />
In this image you see that when you can send the request, you can reconnent this policy in the header. I will tell the browser if the page is from bank32, allow them to get the data because you gave permission. By doing that you will get a response back.
A lot of applications need to lift this limitation because the data they provide need to be dispalyed in a third party website.

## Bypassing Same-Origin Policy
<img width="386" height="250" alt="image" src="https://github.com/user-attachments/assets/6d4376b8-75db-42a3-9d49-27a16ca4ca50" />
Same-origin policy is quite important in protecting the security. There is an interesting attack called DNS Rebinding attack. The way to bypass the same-origin policy is based on the server name (not on the actual IP address). From the name to the IP address, there's a mapping which is called a DNS. You can have control of this mapping, even though you are still taking to your server name. So, you can comply with the same-origin policy. But the actual machine that you talk to, could be somebody else because you control the mapping.
One of the applications of this attack is against iot device. Iot device is usually a step behind a firewall. Making it very difficult for the attack to directly access that. Many iot devices run on a Web server. So if you want to configure the iot, you go through a Web server using HTTP. The communication can only happen inside of your house and can't come from outside. But if the user gets the code or a Web page from the attackers, the code will be inside. Now, you can talk to iot device server. Many iot device have password protected and change periodically. That requires the request from iot server and the response from Ajax code, otherwise you don't get a password. Because of Ajax, the browser is going to actually going to protect that due to it being a cross-origin request. So, you can send a request to ask for the password, but you won't able to get it because of the browser. Getting it from outside is not an option because there's a firewall. These serve as protection layers. But because of how the same-origin policy works and based on the name, attackers can send a request to it own server. The request is still going to it's own server and using it's own name. The browsing will take the attacker's Ajax request. When the response comes in, it will give it to you because you complied with the same-origin policy. But before the actual request goes out, there's a DNS process to find out the IP address for this name. This request goes to the attackers name server because attackers control the mapping of its own computers. The DNS server will tell you, ok you mapped to the attacker's name server because that's how you got my server. Then once you get my code, the attacker will change the mapping. This time, it will map to the iot device with the IP address. Even though still use the name, but the IP address is different. That's why it's called rebinding. That's how you get to talk to the iot device while the server still believes you are talking to the attackers server which is the same-origin policy. Therefore, when the password is returned, you get a password. Then you can set or change the temperature. That is how you can bypass the same-origin policy. The reason this happens is the iot device depends on the same-origin policy to protect it. There are better ways to protect but when relying on same-origin policy, the DNS Rebinding attack could bypass that.

## WebSocket
Another way to talk to the server is called a WebSocket. The Ajax has a limitation. It cannot push data to you
