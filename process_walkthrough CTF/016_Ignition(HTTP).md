
**Ignition (HTB) Walkthrough**

**1. Reconnaissance**

An initial full TCP port scan was performed against the target system to identify all exposed network services.

    nmap -sS -sV -Pn -p- <target-ip>

**Scan options explanation:**

-   -sS performs a TCP SYN scan to identify open ports
-   -sV enables service and version detection
-   -Pn disables host discovery and assumes the host is online
-   -p- scans all 65535 TCP ports

The scan revealed the following open TCP ports:

-   **80 – HTTP**

No additional services were exposed. Since service and version detection was already enabled during the full port scan, no further targeted scans were required.

**2. Web Enumeration**

Port 80 was identified as an HTTP service. The web application was accessed using a browser.

    http://<target-ip>

Upon accessing the site, the application redirected requests to a hostname-based URL (ignition.htb). This behavior indicated the use of virtual host routing.

To resolve the hostname locally, the following entry was added to the /etc/hosts file:

    <target-ip> ignition.htb

After resolving the hostname, the web application loaded correctly.

**3. Directory Enumeration**

Directory enumeration was performed to identify hidden or unlinked endpoints.

    gobuster dir -u http://ignition.htb/ -w /usr/share/wordlists/dirb/common.txt

The enumeration revealed the presence of an administrative endpoint:

    -   /admin

This endpoint hosted an administrative login interface.

**4. Admin Panel Identification**

Accessing the /admin endpoint revealed a web-based administrative login panel.

Visual inspection and page structure indicated that the application was based on Magento, a popular e-commerce platform. Magento administrative panels are commonly targeted due to frequent misconfigurations and weak credential usage.

**5. Authentication Testing**

The login form required a username and password. Testing revealed that the default administrative username admin was valid.

Repeated login attempts did not trigger:

-   Account lockout
-   Rate limiting
-   CAPTCHA challenges

This behavior indicated weak authentication controls and suggested that the login form was vulnerable to brute-force attacks.

**6. Credential Brute-Force Using Burp Suite**

Burp Suite was used to intercept the login request submitted to the admin panel.

-   Burp Proxy was enabled to capture the authentication request.
-   The captured request was forwarded to Burp Intruder.
-   The password parameter was marked as a payload position.
-   A common password wordlist was supplied to Intruder.

Burp Intruder replayed the authentication request multiple times while preserving session cookies and headers.

**7. Authentication Bypass**

During the Intruder attack, one response differed from the others in terms of response length and behavior.

This anomalous response indicated a successful authentication attempt. Using the identified credentials, access to the Magento administrative dashboard was obtained.

**8. Access Confirmation**

Successful login granted full access to the Magento admin interface. This confirmed that unauthorized administrative access had been achieved through exploitation of weak authentication controls.

No further privilege escalation or command execution was required to complete the challenge.

**9. Flag Discovery**

After gaining administrative access, the user flag was accessible through the web application interface.

The flag was retrieved directly from the administrative environment.

**10. Exploitation Outcome**

The system was compromised through exploitation of weak authentication mechanisms in a web-based administrative interface.

The attack relied on:

-   Exposed administrative endpoint
-   Predictable default username
-   Lack of brute-force protection
-   Absence of rate limiting or account lockout controls

This resulted in unauthorized administrative access without requiring authentication bypass exploits, remote code execution, or privilege escalation.


