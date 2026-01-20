**Overview**

**Box:** Preignition  
**OS:** Linux  
**Difficulty:** Very Easy  
**Focus:** Web Enumeration and Weak Authentication

The Preignition machine demonstrates the security risks associated with exposing a web application that relies on weak authentication practices. Through basic service enumeration and directory brute-forcing, an attacker can discover an administrative login page protected only by default credentials, leading to unauthorized access.

**Background Study**

HTTP (Hypertext Transfer Protocol) is an application-layer protocol used for transmitting web content between clients and servers, typically operating over TCP port 80. HTTP is stateless and transmits data in plaintext, making it susceptible to interception and manipulation if additional security controls are not applied.

Modern web applications commonly replace or supplement HTTP with HTTPS, which uses TLS encryption to protect data in transit.

**Attack Approach**

• **Starting access level:** Unauthenticated external attacker  
• **Expected attack surfaces:** Exposed web service

As an unauthenticated attacker, the primary attack surface was an HTTP service running on port 80. Service enumeration identified the web server software, followed by directory brute-forcing to discover hidden resources.

**Key Findings**

• HTTP service exposed on TCP port 80  
• Web server identified as nginx  
• Administrative page (admin.php) discovered via directory brute-forcing  
• Administrative login page secured with default credentials

**Exploitation**

• **Vulnerability type:**  
Weak authentication / Use of default credentials

• **Root cause:**  
The web application exposed an administrative login page that retained default credentials (admin:admin). No additional authentication controls or access restrictions were implemented.

• **Access gained:**  
Authenticated access to the administrative interface, resulting in disclosure of the flag.

**Mitigation / Defense**

This issue could have been mitigated by enforcing strong, unique credentials, removing default usernames and passwords, restricting access to administrative endpoints, and implementing additional security controls such as rate limiting or multi-factor authentication.

**Lessons Learned**

• Default credentials represent a significant security risk  
• Directory enumeration can expose sensitive application components  
• Administrative interfaces must be properly secured  
• Basic reconnaissance is often sufficient to compromise poorly configured systems  
• Secure configuration practices are essential for publicly exposed services