**Bike (HTB) Walkthrough**

**1. Reconnaissance**

An initial full TCP port scan was performed against the target system to identify all exposed network services.

    nmap -sS -sV -Pn -p- <target-ip>

**Scan options explanation:**

-   -sS performs a TCP SYN scan to identify open ports
-   -sV enables service and version detection
-   -Pn disables host discovery and assumes the host is online
-   -p- scans all 65535 TCP ports

**The scan revealed the following open TCP ports:**

-   **80 – HTTP**

Since service and version detection was already enabled during the full port scan, no additional individual port scans were required.

**2. Web Enumeration**

Port 80 was identified as an HTTP service. The web application was accessed using a browser.

    http://<target-ip>

The application presented a simple web interface with a user input field that appeared to be reflected in server responses, indicating possible server-side rendering of user input.

Basic directory and endpoint enumeration was performed but did not reveal additional attack surfaces. Focus shifted to testing the input field for injection vulnerabilities.

**3. Server-Side Template Injection (SSTI) Testing**

Initial SSTI testing was performed by submitting arithmetic expressions into the input field.

Example payload:

    {{7*7}}

Instead of returning a rendered value or reflected output, the application returned a server error page.  
This behavior suggested that the input was being processed by a server-side template engine rather than simple string concatenation.

**4. Template Engine Fingerprinting**

The error message returned by the application revealed internal stack traces and error details.

From the error output, the following was identified:

-   The backend was built using Node.js
-   The template engine in use was Handlebars

This confirmed the presence of a Server-Side Template Injection vulnerability within a Handlebars-rendered template.

**5. SSTI Payload Development**

Publicly known Handlebars SSTI payloads were researched to determine viable sandbox escape techniques.

Initial attempts to directly access Node.js internals using require failed, as require is not exposed as a global object within the Handlebars template context.

Further testing revealed that the global process object was accessible from within the template environment.

**6. Template Sandbox Escape**

Using access to the process object, indirect access to require was achieved via process.mainModule.require.

The following payload structure was used to load the child_process module:

    {{this.constructor.constructor('return process')().mainModule.require('child_process').exec('whoami')}}

The payload successfully executed a system command, confirming remote code execution on the target system.

**7. Payload Delivery via Burp Suite**

Burp Suite was used to intercept and modify HTTP requests to precisely control payload formatting.

Due to HTML encoding and client-side filtering, the SSTI payload required URL encoding before submission to ensure correct server-side interpretation.

Burp Repeater was used to resend the modified request containing the encoded payload.

**8. Command Execution Confirmation**

The response from the server included command execution output, confirming successful exploitation.

Additional commands were executed to validate shell-level access, such as:

    -   whoami
    -   uname -a

This confirmed that commands were being executed with the privileges of the Node.js application process.

**9. Flag Discovery**

Using command execution, the filesystem was enumerated to locate the user flag.

Typical enumeration steps included:

-   Navigating user directories
-   Searching for flag files

The flag was successfully located and read via command execution.

**10. Exploitation Outcome**

The system was compromised through exploitation of a Server-Side Template Injection vulnerability in a Node.js Handlebars application.

The attack relied on:

-   Unsafe server-side rendering of user input
-   Verbose error messages leaking template engine details
-   Inadequate sandboxing of the Handlebars template engine

This allowed a full sandbox escape and resulted in remote code execution without authentication or privilege escalation.