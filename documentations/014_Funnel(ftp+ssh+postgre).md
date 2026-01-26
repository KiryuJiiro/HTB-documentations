# Overview

**Box:** Funnel  
**OS:** Linux  
**Difficulty:** Very Easy  
**Focus:** Anonymous FTP → Credential Reuse → SSH Access → Local Service Enumeration → SSH Port Forwarding → Database Access  

The Funnel machine demonstrates how exposed services and credential reuse can lead to system compromise even when sensitive services are not directly accessible from the network. By leveraging anonymous FTP access to obtain internal credentials, an attacker can gain SSH access and enumerate services bound to localhost. SSH port forwarding is then used to interact with an internal PostgreSQL database, allowing retrieval of sensitive data. This box highlights the importance of internal service exposure and the risks of relying on network isolation alone.

---

# Background Study

## FTP (File Transfer Protocol)

FTP is a legacy protocol used to transfer files between systems. It often supports anonymous authentication, allowing users to access files without credentials. While sometimes intentional, anonymous FTP frequently exposes sensitive information such as configuration files, credentials, or internal documentation.

## Localhost-Bound Services

Services bound to `127.0.0.1` are only accessible from the local system. While this prevents direct external access, it does not protect the service if an attacker gains local access. Such services are invisible to external scanning tools like Nmap.

## SSH Port Forwarding

SSH port forwarding allows traffic from a local machine to be securely tunneled to a remote system.

### Local Port Forwarding (-L)

Local port forwarding maps a port on the attacker’s machine to a port on the target machine. Traffic sent to the local port is forwarded through the SSH tunnel and delivered to a service running on the target, often bound to `127.0.0.1`.

This technique is used when a service is running locally on the compromised host and needs to be accessed externally, such as a database bound to localhost.

### Remote Port Forwarding (-R)

Remote port forwarding exposes a port on the target machine and forwards incoming traffic back to a port on the attacker’s machine. This is typically used to allow the compromised host to access a service running on the attacker’s system, such as receiving reverse shells or callbacks when direct outbound access is restricted.

While not used in this box, remote port forwarding is common in scenarios where inbound access to the attacker is blocked by firewalls or NAT.

### Dynamic Port Forwarding (-D)

Dynamic port forwarding creates a SOCKS proxy through the SSH connection. Instead of forwarding a single service, this allows the attacker to route arbitrary traffic through the compromised host.

Tools configured to use the SOCKS proxy can dynamically access internal networks and multiple local-only services without setting up individual tunnels. This approach is useful for broad internal enumeration and pivoting.

Understanding the differences between local, remote, and dynamic port forwarding is essential for accessing restricted services after gaining initial access.

---

# Attack Approach

- **Starting access level:** Unauthenticated external attacker  
- **Expected attack surfaces:**
  - FTP service (port 21)
  - SSH service (port 22)

Initial reconnaissance focused on identifying exposed network services. Enumeration revealed anonymous FTP access, which provided internal information leading to credential reuse and SSH access. Further enumeration identified a locally hosted database service requiring port forwarding for interaction.

---

# Key Findings

- FTP service exposed with anonymous login enabled  
- Internal files contained usernames and a default password  
- SSH allowed authentication using reused credentials  
- PostgreSQL database bound to localhost (`127.0.0.1:5432`)  
- SSH local port forwarding enabled external database access  
- Database contained sensitive information, including the flag  

---

# Exploitation

## Vulnerability Type

Anonymous FTP Exposure → Credential Reuse → Local Service Exposure via SSH Port Forwarding

## Root Cause

Anonymous FTP access allowed retrieval of internal files containing valid usernames and a default password. One user account still used the default credentials, enabling SSH access. A PostgreSQL service was running locally and relied on network isolation for security. Once SSH access was obtained, port forwarding exposed the database to the attacker.

## Attack Execution

Initial port scanning identified FTP and SSH services. Anonymous FTP login was permitted, allowing retrieval of internal files. One file contained a list of potential usernames, while another revealed a default password.

Credential reuse was tested against the SSH service, resulting in successful authentication for a user account still using the default password. After logging in via SSH, local enumeration revealed a PostgreSQL service listening on `127.0.0.1:5432`.

Because the database was not externally accessible, SSH local port forwarding was used to tunnel the service to the attacker’s machine. Using the PostgreSQL client, the attacker connected to the forwarded port, enumerated available databases and tables, and retrieved the flag from the database.

---

# Access Gained

Authenticated SSH access as a valid user was achieved, followed by access to a locally hosted PostgreSQL database through SSH port forwarding. This allowed retrieval of sensitive data without exploiting any software vulnerability.

---

# Mitigation / Defense

This attack could have been prevented through basic security controls:

- Disabling anonymous FTP access  
- Avoiding storage of credentials in plaintext files  
- Enforcing strong, unique passwords for all users  
- Auditing services bound to localhost  
- Restricting database access with authentication and network controls  
- Monitoring for credential reuse across services  

Security through isolation alone is insufficient once local access is obtained.

---

# Lessons Learned

- Anonymous FTP access often leads to credential disclosure  
- Credential reuse enables lateral access without exploitation  
- Services bound to localhost are still vulnerable after compromise  
- Nmap does not reveal internal services running on compromised hosts  
- SSH port forwarding is a critical post-exploitation technique  
- Database access can be achieved without exploiting the database  
