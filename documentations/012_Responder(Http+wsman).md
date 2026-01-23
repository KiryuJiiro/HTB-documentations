
**Overview**

**Box:** Responder  
**OS:** Windows  
**Difficulty:** Very Easy  
**Focus:** LFI → NTLM Hash Capture (Responder) → Credential Cracking → WinRM Access

The Responder machine demonstrates how multiple individually “low‑impact” misconfigurations can be chained to fully compromise a Windows system. By abusing a Local/Remote File Inclusion vulnerability in a web application, an attacker can coerce the Windows server into authenticating to a malicious listener, leak NTLM credentials, crack them offline, and gain full administrative access via WinRM.

**Background Study**

**Local File Inclusion (LFI)**

LFI is a vulnerability where a web application includes files based on user-supplied input without proper validation. This allows attackers to load unintended local files from the server’s filesystem. While LFI is commonly associated with reading sensitive files, it can also be abused to trigger unintended behaviors when combined with operating system features.

**Remote File Inclusion (RFI)**

RFI occurs when an application allows inclusion of files from remote locations. In modern environments, classic RFI (executing remote PHP files) is often mitigated. However, on Windows systems, supplying a UNC path (\\IP\share) can still trigger outbound network authentication, even if the remote file does not exist or execute.

**UNC Paths**

A UNC (Universal Naming Convention) path is used by Windows to reference network resources:

    \\SERVER\SHARE\file

When Windows encounters a UNC path, it assumes the resource is a network share and attempts to authenticate automatically using NTLM. This behavior occurs before file access or permission checks.

**NTLM Authentication**

NTLM is a legacy Windows authentication protocol. During authentication, a challenge–response hash is exchanged instead of plaintext credentials. While this hash cannot be reversed directly, it can be cracked offline or reused in certain pass‑the‑hash scenarios. NTLM is still widely enabled for backward compatibility.

**Responder**

Responder is a network poisoning and credential capture tool. It listens for authentication requests over protocols such as SMB and HTTP and captures NTLM hashes when systems attempt to authenticate to it. In this attack, Responder passively captured credentials after the target system initiated outbound authentication.

**WinRM (Windows Remote Management)**

WinRM is Microsoft’s remote management service, built on the WS-Management (WSMan) protocol. It allows authenticated users to execute commands and manage Windows systems remotely, commonly via PowerShell. Tools like Evil‑WinRM provide an interactive shell once valid credentials are obtained.

**Attack Approach**

• **Starting access level:** Unauthenticated external attacker  
• **Expected attack surfaces:**

-   HTTP web application (port 80)
-   WinRM service (port 5985)

Initial reconnaissance focused on exposed network services. The presence of an HTTP service and an open WinRM port suggested the possibility of credential-based compromise rather than direct exploitation.

**Key Findings**

• HTTP service exposed on TCP port 80 hosting a PHP-based web application  
• Web application used a page parameter to dynamically include language files  
• Application vulnerable to file inclusion due to insufficient input validation  
• Windows server automatically authenticated to remote UNC paths via NTLM  
• WinRM service exposed on TCP port 5985  
• Administrator credentials reused across services

**Exploitation**

**Vulnerability Type**

File Inclusion Abuse Leading to NTLM Credential Disclosure and Credential Reuse

**Root Cause**

The web application accepted user-controlled input to dynamically include files without proper validation. On a Windows system, this allowed the inclusion of a remote UNC path, causing the server to automatically initiate NTLM authentication to the attacker-controlled host. The leaked NTLM credentials were further exploitable due to weak password hygiene and exposure of the WinRM service.

**Attack Execution**

Initial port scanning identified an HTTP service on port 80 and a WinRM (WSMan) service on port 5985. Accessing the web application revealed a file inclusion parameter used for language selection. By modifying this parameter to reference a remote UNC path pointing to the attacker system, the Windows server attempted to authenticate to the remote resource.

A Responder listener captured the NTLM authentication hash during this outbound authentication attempt. The captured hash was cracked offline using John the Ripper with a common wordlist. The recovered Administrator credentials were then used to authenticate to the target system via WinRM using Evil‑WinRM.

**Access Gained**

Full administrative access to the Windows system was obtained using valid credentials recovered through NTLM hash capture and offline password cracking, allowing complete system compromise and flag retrieval.

**Mitigation / Defense**

This attack could have been prevented through multiple defensive measures:

-   Proper input validation to prevent file inclusion vulnerabilities
-   Disabling or restricting NTLM authentication, especially outbound NTLM
-   Blocking outbound SMB traffic at the firewall level
-   Enforcing strong password policies to prevent offline cracking
-   Limiting WinRM exposure and restricting access to trusted hosts
-   Implementing SMB signing and modern authentication protocols (Kerberos)

Defense-in-depth is critical, as no single misconfiguration alone caused the compromise.

**Lessons Learned**

• File inclusion vulnerabilities can have severe impact beyond file disclosure  
• Windows automatically authenticating to network resources is dangerous  
• NTLM hashes can be captured without exploiting authentication mechanisms  
• Credential theft often enables “legitimate” remote access rather than exploits  
• WinRM is a powerful post-compromise vector when credentials are exposed  
• Multiple low-severity issues can combine into full system compromise