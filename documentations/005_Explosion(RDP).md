**Overview**

**Box:** Explosion  
**OS:** Windows  
**Difficulty:** Very easy  
**Focus:** Remote Desktop Protocol (RDP) Misconfiguration

The Explosion machine demonstrated the security risks associated with exposing Remote Desktop Protocol (RDP) services to untrusted networks without proper authentication controls. A misconfigured RDP service allowed attackers to gain full administrative access to the system.

**Background Study**

Remote Desktop Protocol (RDP) is a Microsoft protocol used to remotely access and manage Windows systems over a network. It typically operates on TCP port 3389 and provides full graphical desktop access.

When RDP is exposed externally without strong authentication mechanisms, such as enforced passwords and Network Level Authentication (NLA), attackers may gain unauthorized access to the system. Insecure RDP configurations are a common cause of Windows system compromise.

**Attack Approach**

• **Starting access level:** Unauthenticated external attacker  
• **Expected attack surfaces:** Exposed remote access services

As an unauthenticated attacker, the primary attack surface was an exposed RDP service. Enumeration of open ports revealed TCP port 3389 open, indicating that remote desktop access was available from the network.

**Key Findings**

• RDP service exposed on TCP port 3389  
• Remote login enabled for the Administrator account  
• No password enforcement for the Administrator account  
• Full graphical desktop access available upon connection

**Exploitation**

• **Vulnerability type:**  
Insecure remote access configuration / Missing authentication

• **Root cause:**  
The Windows system was configured to allow RDP access to an Administrator account without enforcing password-based authentication, permitting unrestricted remote access.

• **Access gained:**  
Full administrative access to the Windows operating system, allowing unauthorized users to interact with the system and retrieve sensitive information (flag).

**Mitigation / Defense**

This issue could have been prevented by enforcing strong passwords for all remote accounts, enabling Network Level Authentication (NLA), restricting RDP access to trusted IP ranges, and disabling public exposure of RDP services.

Remote access services such as RDP should not be directly exposed to untrusted networks without proper security hardening.

**Lessons Learned**

• Exposed RDP services present a critical security risk  
• Proper authentication is essential for remote access protocols  
• Network-level restrictions significantly reduce attack surface  
• Service enumeration can quickly reveal severe misconfiguration