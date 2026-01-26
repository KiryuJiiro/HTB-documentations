**Funnel (HTB) Walkthrough**

**1. Reconnaissance**

An initial full TCP port scan was performed against the target system to identify all exposed network services.

    nmap -sS -sV -Pn -p- <target-ip>

Scan options explanation:  
• -sS performs a TCP SYN scan to identify open ports  
• -sV enables service and version detection  
• -Pn disables host discovery and assumes the host is online  
• -p- scans all 65535 TCP ports

The scan revealed the following open TCP ports:  
• 21 – FTP  
• 22 – SSH

Since service and version detection was already enabled during the full port scan, no additional individual port scans were required.

**2. FTP Enumeration**

Port 21 was identified as an FTP service. The service allowed anonymous authentication, indicating potential misconfiguration.

Anonymous login was performed:

    ftp <target-ip>

Credentials used:

-   Username: anonymous
-   Password: _(blank / any value)_

Anonymous access was granted successfully.

**3. File Retrieval via FTP**

After gaining access to the FTP service, the available files were enumerated and downloaded for local inspection.

    ls
    
    mget *

Two files were retrieved:

-   A mail file containing a list of internal usernames
-   A notice file containing a default password

These files indicated possible credential reuse across system accounts.

**4. Credential Reuse Testing**

The discovered usernames and default password were tested against the SSH service to identify valid credentials.

Credential validation was performed using CrackMapExec:

    cme ssh <target-ip> -u users.txt -p <default-password>

The output confirmed that one user account was still using the default password, allowing SSH authentication.

**5. Initial Access via SSH**

Using the valid credentials, SSH access was obtained:

    ssh <username>@<target-ip>

Authenticated access to the system was successfully established.

**6. Local Service Enumeration**

Once logged in, local enumeration was performed to identify services running on the system that were not exposed externally.

Listening TCP services were enumerated using:

    ss -tln

The output revealed a PostgreSQL database listening on:

127.0.0.1:5432

This service was bound to localhost and therefore inaccessible from the network.

**7. SSH Local Port Forwarding**

To access the locally bound PostgreSQL service, SSH local port forwarding was configured.

    ssh -L 5433:127.0.0.1:5432 <username>@<target-ip>

This forwarded the target’s local PostgreSQL service to port 5433 on the attacker’s machine.

**8. Database Access**

With the port forwarding in place, the PostgreSQL client was used from the attacker’s machine to connect to the database.

    psql -h 127.0.0.1 -p 5433 -U username

Database enumeration commands used:

    \l  -- list databases
    
    \c <db>  -- connect to database
    
    \dt  -- list tables

**9. Flag Discovery**

A table containing the flag was identified. The flag was retrieved using a SQL query:

    SELECT * FROM flag;

The flag was successfully extracted from the database.

**10. Exploitation Outcome**

The system was compromised through a combination of anonymous FTP access, credential reuse, and reliance on network isolation for sensitive services.

Although PostgreSQL was not exposed externally, SSH access enabled local service enumeration and port forwarding, allowing full access to the database. No software vulnerabilities or privilege escalation techniques were required.




