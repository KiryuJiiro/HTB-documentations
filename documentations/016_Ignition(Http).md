# Ignition – Hack The Box Write-up

## Overview
**Box:** Ignition  
**OS:** Linux  
**Difficulty:** Easy  
**Focus:** Web Enumeration → Virtual Host Resolution → Admin Panel Discovery → Weak Credential Exploitation  

The Ignition machine demonstrates how insecure web configurations and weak administrative credentials can lead to unauthorized access. By enumerating a web server, identifying a hidden administrative interface, and exploiting poor authentication practices, an attacker can gain access to sensitive application functionality. This box highlights the risks of exposed admin panels, lack of brute-force protection, and reliance on weak or default credentials in web applications.

---

## Background Study

### Web Enumeration
Web enumeration involves discovering hidden directories, files, and endpoints exposed by a web server. Tools such as Gobuster are commonly used to brute-force directory paths and identify administrative or sensitive interfaces that are not directly linked from the main application.

### Virtual Host Resolution
Some web applications rely on hostname-based virtual hosting. If the correct hostname is not resolved locally, the application may redirect or fail to load properly. Modifying the `/etc/hosts` file allows an attacker to resolve internal domain names to the target IP address.

### Weak Authentication Mechanisms
Weak authentication includes the use of default usernames, predictable passwords, lack of account lockout, and absence of rate limiting. These flaws allow attackers to perform brute-force or credential-stuffing attacks with minimal resistance.

### Burp Suite
Burp Suite is used to intercept, analyze, and replay HTTP requests. In authentication attacks, Burp Intruder can be used to automate repeated login attempts by mutating request parameters while preserving session data and headers.

---

## Attack Approach

- **Starting access level:** Unauthenticated external attacker  
- **Expected attack surfaces:**
  - HTTP service (port 80)

Initial reconnaissance focused on identifying exposed network services and enumerating the web application. The attack targeted the authentication mechanism of the administrative interface discovered during web enumeration.

---

## Key Findings

- Only HTTP (port 80) exposed on the target  
- Web application required hostname resolution (`ignition.htb`)  
- Directory enumeration revealed an administrative login panel  
- Admin interface belonged to a Magento-based application  
- Default administrative username (`admin`) was valid  
- No rate limiting or account lockout on login attempts  
- Weak password protection allowed brute-force attack  
- Burp Intruder successfully identified valid credentials  

---

## Exploitation

### Vulnerability Type
Weak Authentication → Brute-Force Attack → Unauthorized Administrative Access  

### Root Cause
The administrative login interface was publicly accessible and protected only by weak credentials. The application lacked basic security controls such as rate limiting, CAPTCHA, or account lockout mechanisms. Additionally, the use of a predictable default username significantly reduced the attack complexity.

### Attack Execution
Initial scanning identified port 80 as the only exposed service. Accessing the web application resulted in a redirect to a hostname-based URL, requiring manual addition of the domain name to the local `/etc/hosts` file.

Directory brute-forcing revealed the presence of an `/admin` endpoint, which hosted a Magento administrative login panel. The username `admin` was confirmed to be valid based on server responses.

Burp Suite was used to intercept a legitimate login request. This request was then forwarded to Burp Intruder, where the password parameter was marked as a payload position. A common password wordlist was supplied to Intruder, which replayed the login request multiple times while preserving session cookies and headers.

Differences in HTTP response length and behavior revealed a successful authentication attempt, granting access to the Magento administrative dashboard.

---

## Access Gained
Unauthorized access to the Magento administrative interface was achieved by exploiting weak authentication controls through a brute-force attack.

---

## Mitigation / Defense
This attack could have been prevented through the following controls:

- Enforcing strong, non-default administrative credentials  
- Implementing account lockout after repeated failed login attempts  
- Applying rate limiting to authentication endpoints  
- Adding CAPTCHA mechanisms to admin login pages  
- Restricting access to administrative panels by IP  
- Monitoring and alerting on abnormal login activity  

---

## Lessons Learned

- Exposed admin panels significantly increase attack surface  
- Default usernames greatly reduce brute-force complexity  
- Lack of rate limiting enables automated attacks  
- Burp Intruder is effective for testing weak authentication  
- Response size and behavior are reliable indicators of login success  
- Even simple web misconfigurations can lead to full administrative compromise  
