**Overview**

**Box:** Mongod  
**OS:** Linux  
**Difficulty:** Very Easy  
**Focus:** Database Enumeration and Misconfigured MongoDB Authentication

The Mongod machine highlights the security risks of exposing a MongoDB database service directly to the internet without authentication. By identifying an open MongoDB service through basic enumeration, an attacker can gain unrestricted access to stored databases and retrieve sensitive information.

**Background Study**

MongoDB is a NoSQL, document-oriented database that stores data in collections of JSON-like documents. The MongoDB server daemon, mongod, listens on TCP port 27017 by default.

If authentication is not explicitly enabled and the service is publicly accessible, MongoDB allows unauthenticated users to connect and interact with all available databases. This makes misconfigured MongoDB instances a frequent target for attackers, as exploitation often requires no credentials or advanced techniques.

**Attack Approach**

• **Starting access level:** Unauthenticated external attacker  
• **Expected attack surfaces:** Exposed database service

The primary attack surface was a MongoDB service exposed over TCP. Enumeration focused on identifying open ports and services to determine whether database services were publicly accessible without authentication.

**Key Findings**

• Full port scanning revealed multiple open TCP ports  
• MongoDB service exposed on TCP port 27017  
• MongoDB authentication was disabled  
• Unauthenticated users could access databases and collections  
• Sensitive data was stored in plaintext within collections

**Exploitation**

• **Vulnerability type:**  
Misconfigured database / Missing authentication

• **Root cause:**  
The MongoDB service was exposed to external networks with authentication disabled, allowing unrestricted access to all stored data.

• **Access gained:**  
Unauthenticated access to MongoDB databases and collections, resulting in disclosure of the flag.

**Mitigation / Defense**

This issue could have been mitigated by enabling MongoDB authentication, binding the database service to localhost, restricting access through firewall rules, and avoiding direct exposure of database services to the public internet. Network segmentation and proper access controls should be enforced for database servers.

**Lessons Learned**

• MongoDB does not enable authentication by default  
• Databases should never be directly exposed to the internet  
• Full port scans can reveal non-web attack surfaces  
• Misconfiguration is a common and critical security weakness  
• Basic enumeration is often sufficient to compromise poorly secured systems