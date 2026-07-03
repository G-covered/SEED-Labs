## JavaScript and Sandbox

JavaScript is dynamic content. There is a program inside that web site. Those web sites maybe not trusted. The code inside the page will go to your computer and acuatlly run inside your computer. That may bring risk.
For exmaple: You download a file off the web site. It runs inside your computer and you get a virus.
Everyday we visit sites and bring untrusted code to run inside our computer. How does the computer deal with the security risk.

### JavaScrpt Engine
<img width="327" height="304" alt="image" src="https://github.com/user-attachments/assets/e4e6a8cb-5de5-4800-8cf9-96fa360044ac" />


When the code gets to your browser, the code needs to be compiled. They all have to be interpreted by your browser. In inside your browser, this JavaScript engine actually compiles this into bytecode. Then engine runs the bytecode and will enforce the security policies inside the engine. The sandbox is built-in to the engine.
For different browsers, there are different engines but internally and what they do are similar. Complie, run bytecode, and enforce security polices.

### Security Consideration
<img width="379" height="355" alt="image" src="https://github.com/user-attachments/assets/32479dc1-f4c0-4dd1-81a1-7ca05e10c0ac" />
Why can't we just use binary code? What can't invent a binary to run the code derectly?
Through a sercurity prespective, it makes it very difficult to run security policy through binary.
The binary runs derectly on top of the CPU. This means that using binary instruction, you can access anything in the system. The binary becomes part of your browser. The OS has no idea which part belongs to the browser or which part is untrusted code. This makes it difficult to protect for secure reasons.

On the other hand, there is an engine between the bytecode and the OS before reaching the CPU. Now the engine is decides whether you have access or not.

### Behaviors of Sandboxing
<img width="386" height="349" alt="image" src="https://github.com/user-attachments/assets/079bf3ff-02b6-4ff5-9f01-f6c3f07c5bcd" />
1 JavaScript Code
First, if you want to access a page, JavaSript code will decide whether you can access the content insid the page or not.
2 Other Pages
You may want to access another page. Will this be allowed and what are the security polices on that.
3 Browser Data (e.g. Cookies, History, etc.)
If you to access browser date, allowed?
4 System Data (e.ge. files)
Similarly, do you want to acess the file?
5 Network
Finally, do you want to talk to the network, servers, and what is the security restirction on that.
