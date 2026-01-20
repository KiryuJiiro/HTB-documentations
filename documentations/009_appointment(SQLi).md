**Overview**

**Box:** Appointment  
**OS:** Linux  
**Difficulty:** Very Easy  
**Focus:** SQL Injection / Authentication Bypass

The Appointment machine demonstrates the risks associated with improper handling of user input in web applications. A vulnerable login mechanism allows attackers to manipulate backend SQL queries, resulting in authentication bypass without valid credentials.

**Background Study**

**SQL (Structured Query Language)** is a standard language used to interact with relational databases. Web applications commonly rely on SQL databases to store and retrieve user credentials, session data, and application content.

**SQL Injection (SQLi)** is a vulnerability that occurs when user input is directly incorporated into SQL queries without proper validation or sanitization. By injecting SQL syntax into input fields, an attacker can alter the logic of backend queries. This may allow unauthorized access, authentication bypass, or data disclosure. SQL injection is an application-layer vulnerability and is independent of the underlying web server or operating system.

**Attack Approach**

- **Starting access level:** Unauthenticated external attacker
- **Expected attack surfaces:** HTTP web application (login functionality)

As an unauthenticated attacker, the primary attack surface was a publicly accessible web service hosted on port 80. Initial reconnaissance focused on identifying exposed services, followed by basic web enumeration to locate application entry points.

**Key Findings**

- HTTP service exposed on TCP port 80
- Apache web server hosting a PHP-based web application
- Login page accessible without authentication
- Backend SQL query vulnerable to injection
- Authentication logic could be bypassed by manipulating user input

**Exploitation**

**Vulnerability Type**

SQL Injection / Authentication Bypass

**Root Cause**

User-supplied input was directly concatenated into an SQL query without sanitization or prepared statements. This allowed attackers to terminate the SQL string and comment out password validation logic, effectively bypassing authentication.

**Access Gained**

Unauthorized authenticated access to the web application as an existing user without providing a valid password, resulting in disclosure of the flag.

**Mitigation / Defense**

This issue could have been mitigated by using prepared statements or parameterized queries, implementing strict input validation, and avoiding direct string concatenation in SQL queries. Authentication logic should ensure proper verification of credentials and handle database responses securely. Web applications should follow secure coding practices to prevent injection vulnerabilities.

**Lessons Learned**

- SQL injection is an application-layer vulnerability independent of network configuration
- Improper input handling can completely bypass authentication mechanisms
- Common usernames such as "admin" are frequent targets during attacks
- Service and application enumeration are critical early attack steps
- Secure coding practices are essential for protecting backend databases