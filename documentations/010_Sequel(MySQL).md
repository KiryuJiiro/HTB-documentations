## Overview
**Box:** Sequel  
**OS:** Linux  
**Difficulty:** Very easy  
**Focus:** MariaDB / MySQL Misconfiguration  

The Sequel machine demonstrated the security risks of exposing a database service directly to the network without proper authentication controls. An unauthenticated attacker was able to connect to the MariaDB service and enumerate databases to retrieve sensitive information.

---

## Background Study
MariaDB is an open-source relational database management system and a community-driven fork of MySQL. It uses SQL for data definition and manipulation. When database services are exposed to external networks without authentication or access restrictions, attackers can directly interact with stored data, leading to data disclosure and full system compromise.

---

## Attack Approach
- **Starting access level:** Unauthenticated external attacker  
- **Expected attack surfaces:** Exposed database service  

As an unauthenticated attacker, the primary attack surface was the exposed MariaDB service running on its default port. The attack focused on identifying open network services and attempting direct access to the database without valid credentials.

---

## Key Findings
- MariaDB/MySQL service exposed on TCP port **3306**
- No authentication required for the **root** database user
- Sensitive data stored in plaintext within database tables

---

## Exploitation
- **Vulnerability type:**  
  Insecure service configuration / Missing authentication  

- **Root cause:**  
  The MariaDB service was configured to allow remote root access without a password. This misconfiguration enabled unauthenticated users to connect directly to the database service and enumerate its contents.

- **Access gained:**  
  Unauthorized database access with root-level privileges  

Once connected, the attacker was able to enumerate all available databases, inspect tables within each database, and extract sensitive information. The flag was retrieved from one of the database tables using standard SQL queries.

---

## Mitigation / Defense
This issue could have been prevented by restricting database access to trusted hosts only, enforcing strong authentication for all database users, and disabling remote root login. Database services should not be exposed directly to untrusted networks and should be protected using firewalls and proper access controls.

---

## Lessons Learned
- Exposed database services present a critical attack surface
- Default or missing authentication can lead to immediate compromise
- Databases should never be directly accessible from untrusted networks
- Proper service hardening and network segmentation are essential for security
