**Sequel (HTB) Walkthrough**

**1\. Reconnaissance**

An initial TCP port scan was performed against the target system to identify exposed network services.

nmap -sS -sV -Pn -p- &lt;target-ip&gt;

Scan options explanation:  
• -sS performs a TCP SYN scan to identify open ports  
• -sV enables service and version detection  
• -Pn disables host discovery and assumes the host is online  
• -p- scans all 65,535 TCP ports

The scan revealed TCP port 3306 open, indicating the presence of a MariaDB/MySQL database service exposed directly to the network.

**2\. Service Identification**

The open port 3306 was identified as a MariaDB database service. Since database services should normally be restricted to localhost or internal networks, further investigation was required to determine whether authentication controls were enforced.

**3\. Database Enumeration**

A direct connection attempt was made to the MariaDB service using the MySQL client with the root user.

mysql -h &lt;target-ip&gt; -u root

The connection succeeded without requiring a password, confirming that the database service was misconfigured and allowed unauthenticated remote access.

**4\. Unauthorized Database Access**

Once connected, database enumeration was performed to identify available databases.

SHOW DATABASES;

Multiple databases were listed, indicating that the attacker had full visibility into the database server.

**5\. Table Enumeration**

Each database was inspected individually to enumerate tables and stored data.

USE &lt;database_name&gt;;

SHOW TABLES;

Tables within the databases were enumerated to identify potentially sensitive information.

**6\. Data Extraction**

SQL queries were executed against the identified tables to retrieve stored records.

SELECT \* FROM &lt;table_name&gt;;

By querying the contents of the tables, sensitive information was disclosed. The challenge flag was located within one of the database tables and successfully retrieved.

**7\. Flag Discovery**

The flag was extracted directly from the database table output. No further exploitation was required, as full database access had already been achieved due to the misconfiguration.

**8\. Exploitation Outcome**

The system was compromised due to an exposed MariaDB service with missing authentication controls. An unauthenticated attacker was able to connect remotely as the root database user, enumerate databases, extract sensitive information, and retrieve the flag.