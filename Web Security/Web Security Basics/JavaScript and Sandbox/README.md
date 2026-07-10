## JavaScript and Sandboxing

JavaScript is inherently dynamic, meaning a website runs actual program code directly inside your web browser. Because we visit untrusted websites every day, letting external code run on a local computer presents massive security risks (similar to downloading and executing a malicious file). 

To mitigate this risk, web browsers rely on a **sandbox** architecture to restrict what this untrusted code can do.

### The JavaScript Engine
<img width="327" height="304" alt="image" src="https://github.com/user-attachments/assets/e4e6a8cb-5de5-4800-8cf9-96fa360044ac" />

When JavaScript arrives at your browser, it must be interpreted and executed. 
*   The browser's **JavaScript Engine** compiles the source code into intermediate **bytecode**.
*   The engine executes this bytecode while strictly enforcing security policies. 
*   The sandbox environment is built directly into this engine layer.
*   While different browsers use different underlying engines (e.g., V8 in Chrome, SpiderMonkey in Firefox), they all follow this same lifecycle: compile source, execute bytecode, and enforce security boundaries.

### Security Consideration: Why Not Native Binary?
<img width="379" height="355" alt="image" src="https://github.com/user-attachments/assets/32479dc1-f4c0-4dd1-81a1-7ca05e10c0ac" />

Why don't we just pre-compile web code into native binaries to let it run directly on the machine? From a security standpoint, enforcing safety policies on raw binary code is extremely difficult.
*   **Binary Code:** Runs directly on top of the CPU. Native binary instructions can potentially access any memory address or system resource. If a browser executes a raw binary, that binary inherits the browser's system privileges. The Operating System (OS) cannot distinguish between the safe browser process and the untrusted web code.
*   **Bytecode + Engine:** By placing a managed JavaScript engine between the bytecode and the OS, the engine acts as an explicit gatekeeper. The engine evaluates every action before it ever reaches the CPU, deciding whether the code has permission to perform it.

### Behaviors of Sandboxing
<img width="386" height="349" alt="image" src="https://github.com/user-attachments/assets/079bf3ff-02b6-4ff5-9f01-f6c3f07c5bcd" />

The browser sandbox evaluates and enforces security policies across five distinct boundaries:
1.  **Current Page Content:** Controls what the script can manipulate on the active page.
2.  **Other Pages:** Restricts scripts from interacting with pages open in other tabs or origins.
3.  **Browser Data:** Limits access to sensitive user data like cookies, storage, and browsing history.
4.  **System Data:** Restricts direct access to the local filesystem.
5.  **Network:** Enforces policies on how scripts can communicate back to web servers.

### JavaScript Accessing Page Contents
<img width="396" height="479" alt="image" src="https://github.com/user-attachments/assets/745dfa57-958a-423c-97a6-515c85e9070d" />

When a browser renders a web page, it parses the HTML to construct the **Document Object Model (DOM)**—a hierarchical tree structure where every element is a node. 

The browser exposes APIs that allow JavaScript to query and alter this tree:
*   **Finding Nodes:** Methods like `document.getElementById()` allow JavaScript to locate a specific node.
*   **Modifying Nodes:** Properties like `innerHTML` let code read or change the actual contents of a node, allowing scripts to dynamically add or delete elements.

Everything stems from the root `document` object. The JavaScript executing on a page is granted a reference to this root object, giving it complete access to that specific tree. However, this access is isolated; a script cannot access the root document of a page running in a different tab.

#### The iFrame Complication
Things get trickier when a page contains an `iframe` (an embedded HTML document inside another HTML document). This sets up a scenario with multiple distinct pages loaded in a single viewport, introducing complex security puzzles:
*   Can a parent page access the DOM of a child iframe?
*   Can a child iframe access its parent?
*   Can sibling iframes interact with each other?

>  **Security Connection:** These specific boundary questions form the baseline mechanics for **Clickjacking** attacks, which rely heavily on manipulating nested iframes. Without iframes, DOM security is simple: one isolated tree per page.

### HTML Objects Examples
Beyond directly manipulating the DOM tree, scripts can interact with specific built-in page objects:
*   `document.cookie` — Accesses the cookies associated with the current page.
*   `document.images` — A collection of all images on the page.
*   `document.forms` — A collection of all interactive forms on the page.
*   `document.URL` — Evaluates the current page string.

### Accessing Browser Data
<img width="360" height="362" alt="image" src="https://github.com/user-attachments/assets/4e860ac5-a71f-499b-8f06-b2716855d4d3" />

While JavaScript is entitled to read data that came directly from the web server, it is *not* entitled to read private user data stored inside the browser itself. Browsers enforce strict access controls using the global `window` object:

*   `window.history` — Contains the user's browsing path. To preserve privacy, JavaScript cannot read the actual URLs inside your history; it is only allowed to trigger actions like the `.back()` and `.forward()` APIs.
*   `window.navigator` — Contains state information about the browser and user device, including geographic location. Because location data is highly sensitive, the browser's sandbox blocks scripts from accessing it automatically—instead, it prompts the user to explicitly grant or deny permission.

### Accessing Local Files
<img width="373" height="329" alt="image" src="https://github.com/user-attachments/assets/aa33392c-fccf-4d38-ab4d-2d97ed902126" />

To submit forms or upload media, web apps must be able to process local files. However, the sandbox completely bans JavaScript from arbitrarily fetching local files by their path names (e.g., a script cannot silently run `fetch("C:/secrets.txt")`).

Instead, file security relies on the **user acting as the access control middleman**:
*   The page must present an explicit file picker element (like `<input type="file">`).
*   The browser sandbox completely handles this selection window. 
*   JavaScript remains completely blind to the local system until the user manually selects and passes a specific file. Only then does the engine allow the script to read and upload that single, isolated asset.
