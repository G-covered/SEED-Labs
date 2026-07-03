## Web Browser

### Content vs. Presentation
*   **HTML:** Describes the structure and core content of a web page.
*   **CSS:** Focuses entirely on the presentation and styling (e.g., layouts, colors, and fonts).

### Browser Components: Plug-ins vs. Extensions
To provide features that native browsers couldn't initially support, two separate add-on models were created:

*   **Plug-ins (Binary Code)**
    *   Executed as compiled **binary code** (for example, legacy PDF viewers).
    *   **Security Impact:** Because they run as native binaries, they exist "inside the gate." They are not a sandboxed part of the browser and have direct access to system memory. This makes isolating them incredibly difficult, turning legacy plug-ins into a major source of browser vulnerabilities.
*   **Extensions (API-Driven)**
    *   Interact with the browser by requesting information and actions through a restricted **browser API**.
    *   **Security Impact:** Extensions do not have arbitrary memory access or access to protected system gates. They must operate strictly within the permissions granted by the browser's security framework.


### Dynamic Content

Historically, dynamic content was introduced to allow platforms to animate and add interactivity to websites. 

### Legacy Platforms (Deprecated)
*   Adobe Flash
*   Microsoft Silverlight
*   ActiveX
*   Java applets

> ⚠️ **Security Takeaway:** These platforms relied heavily on the risky binary plug-in model, introducing massive security holes. Thanks to modern, native browser capabilities, these legacy third-party platforms are no longer necessary and have been completely phased out.
