**Overview**

Box**:** Dancing  
OS**:** Windows  
Difficulty**:** Very easy  
Focus**:** SMB (Server Message Block)

The Dancing machine demonstrates the security risks of exposing SMB services with improper access controls. Misconfigured SMB shares allowed unauthenticated users to enumerate shared resources and retrieve files without valid credentials, leading to information disclosure.

**Background Study**

SMB (Server Message Block) is a network file-sharing protocol primarily used in Windows environments to provide access to shared files, folders, and resources over a network. SMB operates over TCP port 445 and relies on authentication and access control mechanisms to restrict access. When SMB is misconfigured to allow anonymous or guest access, attackers can enumerate and access shared resources without authentication, resulting in sensitive data exposure.

**Attack Approach**

- **Starting access level:** Unauthenticated external attacker
- **Expected attack surfaces:** Exposed SMB network services

As an unauthenticated attacker, the primary attack surface was the exposed SMB service running on the target system. The goal was to enumerate available SMB shares and identify any resources accessible without authentication.

**Key Findings**

- SMB service exposed on TCP port 445
- SMB service identified as microsoft-ds
- SMB server allowed anonymous authentication
- One or more shared folders were accessible without valid credentials
- Files within a shared directory could be read and transferred remotely

**Exploitation**

- **Vulnerability type:**  
    Insecure service configuration / Improper access control
- **Root cause:**  
    The SMB service was configured to allow anonymous access, enabling unauthenticated users to enumerate shared folders and access files without credential verification.
- **Access gained:**  
    Unauthorized read access to files stored within a shared SMB directory, allowing retrieval of sensitive information.

**Mitigation / Defense**

This issue could be mitigated by disabling anonymous SMB access, enforcing proper authentication mechanisms, and restricting shared resources to authorized users only. SMB shares should follow the principle of least privilege, and unnecessary file-sharing services should be disabled when not required.

**Lessons Learned**

- Misconfigured SMB services pose a significant risk of information disclosure
- Anonymous or guest access should never be enabled on file-sharing services
- Proper enumeration of network services is critical for identifying insecure configurations
- Even basic misconfigurations can lead to full compromise of sensitive data