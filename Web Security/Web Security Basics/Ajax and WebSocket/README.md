## Ajax and Its Security Policy

<img width="320" height="301" alt="image" src="https://github.com/user-attachments/assets/b2f11e94-88dc-4a9e-a9a4-8957326309b0" />

There are three ways to communicate with the server: normal HTTP request, Ajak, and WebSocket.
What is the difference between Ajax and normal HTTP request?
Before Ajax was introduced, it was not easy to update something inside the page using the data from the server.
Example: The quote of stock exchanges always changes every second. To update it, you would need to send a normal HTTP request and a new page will come back with the same information but with the updated part. The new page will come in, the old page will be kicked out, and the new page will be displayed. That's how you saw the contents changed. Making this inefficient and uneven transition.
Ajax changed that and basically does the same thing. To update something, you would send out a Ajax request.
