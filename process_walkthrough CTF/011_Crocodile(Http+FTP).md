## 1. Reconnaissance
An initial TCP port scan was performed against the target system to identify exposed network services.

```
nmap -F <target-ip>
```
The fast scan revealed two open TCP ports:

-   **21** – FTP
    
-   **80** – HTTP
    

To gather additional details, targeted service scans were performed on each open port.
```
nmap -sS -sV -Pn -p 21,80 <target-ip> 
```
Scan options explanation:

-   `-sS` performs a TCP SYN scan to identify open ports
    
-   `-sV` enables service and version detection
    
-   `-Pn` disables host discovery and assumes the host is online
    

The scan confirmed the presence of an FTP service on port 21 and an HTTP web server on port 80.
## 2. Service Identification

Port 21 was identified as an FTP service, and port 80 was identified as an HTTP web service hosting a PHP-based application. Since FTP services commonly expose sensitive files when misconfigured, further investigation of the FTP service was prioritized.
## 3. FTP Enumeration

A connection attempt was made to the FTP service using anonymous authentication.

`ftp <target-ip>` 

The FTP server allowed login with the username `anonymous` and no password, confirming that anonymous FTP access was enabled.

----------

## 4. Sensitive File Disclosure

Once authenticated to the FTP service, directory listing was performed to identify accessible files.

`ls` 

Two files containing credential-related information were discovered. These files were transferred to the attacker’s system for offline inspection.

`get <filename>` 

The downloaded files contained a list of usernames and corresponding passwords, indicating improper storage of sensitive credentials on the FTP server.

----------

## 5. Web Service Enumeration

To identify hidden resources on the web application, directory brute-forcing was performed against the HTTP service.

`gobuster dir -u http://<target-ip> -x php -w <path-to-wordlist>` 

The scan revealed an accessible `login.php` page that was not linked from the main web page.

----------

## 6. Web Authentication Bypass

The discovered login page was accessed via a web browser.

`http://<target-ip>/login.php` 

Credentials obtained from the FTP server were tested against the login form. One of the username and password combinations successfully authenticated, granting access to the protected area of the web application.

----------

## 7. Flag Discovery

After successful authentication, the flag was displayed within the authenticated section of the web application. No further exploitation or privilege escalation was required.

----------

## 8. Exploitation Outcome

The system was compromised due to an exposed FTP service with anonymous access enabled and poor credential management practices. Sensitive credential files were disclosed via FTP and reused to authenticate to a web application, resulting in unauthorized access and retrieval of the flag.