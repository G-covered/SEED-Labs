## Web Server
<img width="426" height="303" alt="image" src="https://github.com/user-attachments/assets/4cca4635-340f-4e46-a83d-171b40bc7de7" />
You have a browser talking to the server using HTTP. On the server side, there are two different components. One is this generic entity server which handles the HTTP request. The other component is the web application server. If you want to generate a page in such a way, it is done inside this web application server. That itself may not be a server, but it is a program where the main logic is at.
If it's a static file, the HTTP server can handle it. It can load the file and return it back to the client.
If it's dynamic content like generating a page, then the HTTP server will not know how to generate that. So, that is where the web application comes in.

The way that HTTP server and web application communicate is through a standard interface called CGI.
For example if a request comes in and needs to fetch something .php, the HTTP server knows that you to run a php script and hands it to the web application. Because of this performance in different processes, it produces a lot of overhead.
So, now everything is build into the ATP server as a module.
For example
