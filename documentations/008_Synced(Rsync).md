**Overview**

**Box:** Synced  
**OS:** Linux  
**Difficulty:** Very Easy  
**Focus:** Insecure rsync Configuration / Information Disclosure

The Synced machine demonstrates the security risks associated with exposing an rsync daemon with anonymous access enabled. Improper service configuration allows unauthenticated attackers to enumerate shared directories and retrieve sensitive files without restriction.

**Background Study**

rsync is a file synchronization utility commonly used for backups and data replication. When operating in daemon mode, rsync listens on TCP port 873 and exposes shared directories referred to as modules.  
If access controls are misconfigured or authentication is not enforced, these modules may be enumerated and accessed by unauthorized users, leading to data disclosure.

**Attack Approach**

• **Starting access level:** Unauthenticated external attacker  
• **Expected attack surfaces:** Exposed rsync service

As an unauthenticated attacker, the primary attack surface was a publicly accessible rsync daemon. Basic service enumeration was sufficient to identify the exposed service and confirm the presence of accessible shared directories.

**Key Findings**

• rsync service exposed on TCP port 873  
• rsync daemon identified through service enumeration  
• Anonymous access enabled  
• Shared rsync modules accessible without authentication  
• Sensitive data stored within exposed directories

**Exploitation**

• **Vulnerability type:**  
Insecure service configuration / Anonymous rsync access

• **Root cause:**  
The rsync daemon allowed unauthenticated users to list and access shared modules, exposing internal files without authorization.

• **Access gained:**  
Read access to files within exposed rsync directories, resulting in disclosure of the flag.

**Mitigation / Defense**

This issue could have been mitigated by disabling anonymous access, enforcing authentication for rsync modules, restricting module visibility, or running rsync exclusively over SSH. Synchronization services should be limited to trusted networks and hardened appropriately.

**Lessons Learned**

• Exposed rsync services present a significant security risk  
• Anonymous access to synchronization daemons should never be enabled  
• Service enumeration alone can lead to sensitive data disclosure  
• Backup and synchronization services are common attack vectors  
• Secure configuration practices are essential for externally exposed services