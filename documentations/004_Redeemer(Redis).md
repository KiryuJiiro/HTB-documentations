**Overview**

**Box:** Redeemer  
**OS:** Linux  
**Difficulty:** Very easy  
**Focus:** Redis (In-Memory Database Misconfiguration)

The Redeemer machine demonstrated the security risks of exposing an unauthenticated Redis service to the network. Improper configuration allowed attackers to directly access sensitive in-memory data without requiring credentials.

**Background Study**

Redis is an in-memory key-value data store commonly used for caching, session management, and fast data retrieval. It operates as a network service, typically on TCP port 6379. If authentication is not enforced and the service is exposed externally, attackers can connect directly and interact with the database, potentially accessing sensitive data.

**Attack Approach**

• **Starting access level:** Unauthenticated external attacker  
• **Expected attack surfaces:** Exposed network services

As an unauthenticated attacker, the primary attack surface was an exposed Redis service. Enumeration of open ports revealed Redis running without authentication, allowing direct interaction with the database.

**Key Findings**

• Redis service exposed on TCP port 6379  
• No authentication required to access the Redis instance  
• Sensitive data stored directly within the Redis database

**Exploitation**

• **Vulnerability type:**  
Insecure service configuration / Missing authentication

• **Root cause:**  
The Redis service was configured to accept remote connections without authentication, allowing any external user to connect and interact with stored data.

• **Access gained:**  
Unauthorized read access to Redis databases, leading to disclosure of sensitive information (flag).

**Mitigation / Defense**

This issue could have been prevented by restricting Redis to localhost, enforcing authentication using strong passwords, implementing network-level access controls, and disabling exposure of Redis to untrusted networks. Redis should not be directly accessible from the internet unless absolutely necessary.

**Lessons Learned**

• Exposed in-memory databases present serious security risks  
• Authentication and network restrictions are critical for backend services  
• Redis should never be exposed publicly without proper security controls  
• Enumeration of services is often enough to compromise poorly configured system