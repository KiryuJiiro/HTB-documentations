# Archetype — HTB Walkthrough Overview

**Box:** Archetype  
**OS:** Windows  
**Difficulty:** Easy  
**Focus:**  
SMB Enumeration → MSSQL Credential Exposure → SQL Access → Command Execution → Reverse Shell → Privilege Escalation  

The **Archetype** machine demonstrates how exposed SMB shares containing sensitive configuration files can lead to database compromise and full system takeover. By extracting MSSQL credentials from a shared configuration file, an attacker can authenticate to the SQL server, enable command execution via stored procedures, and obtain a reverse shell. Further enumeration reveals misconfigurations that allow escalation to Administrator privileges.

This box highlights the dangers of credential exposure, insecure database configurations, and weak privilege separation.

---

## Background Study

### SMB Enumeration
SMB enumeration involves identifying shared network resources and files accessible without authentication. Misconfigured shares often expose sensitive files such as configuration files, credentials, or backups.

### Microsoft SQL Server (MSSQL)
MSSQL is a relational database management system that supports stored procedures capable of executing operating system commands if misconfigured. Features such as `xp_cmdshell` can allow direct command execution on the host when enabled.

### Stored Procedures & xp_cmdshell
`xp_cmdshell` is a built-in MSSQL stored procedure that allows execution of system commands through SQL queries. While disabled by default for security reasons, it is sometimes enabled in insecure environments.

### Reverse Shells
A reverse shell allows the target machine to initiate a connection back to the attacker, providing interactive command execution and bypassing inbound firewall restrictions.

### Privilege Escalation
Privilege escalation on Windows commonly occurs through credential reuse, misconfigured privileges, service accounts with excessive permissions, or leaked credentials stored in system files or command histories.

---

## Attack Approach

- **Starting access level:** Unauthenticated external attacker  
- **Expected attack surfaces:**  
  - SMB (port 445)  
  - MSSQL (port 1433)  

The attack began by enumerating SMB shares, extracting database credentials, and abusing MSSQL features to gain command execution and escalate privileges.

---

## Key Findings

- SMB service exposed without authentication  
- Backup/configuration files accessible via SMB share  
- MSSQL credentials discovered in configuration file  
- MSSQL server accessible using extracted credentials  
- `xp_cmdshell` could be enabled and abused  
- Ability to execute OS commands via SQL  
- Reverse shell obtained as a low-privileged service account  
- Privilege escalation possible through credential discovery  

---

## Exploitation

### Vulnerability Type
Credential Exposure → Misconfigured MSSQL → Command Execution → Privilege Escalation

### Root Cause
Sensitive MSSQL credentials were stored in plaintext within an SMB-accessible configuration file. Once authenticated to MSSQL, the database server allowed enabling `xp_cmdshell`, leading to arbitrary command execution on the underlying Windows system. Poor credential hygiene and improper privilege separation enabled escalation to Administrator.

### Attack Execution
Initial reconnaissance identified SMB as an exposed service. Anonymous access to SMB shares revealed a configuration file containing MSSQL credentials. These credentials were used to authenticate to the MSSQL server using Impacket’s `mssqlclient.py`.

After successful authentication, advanced configuration options were enabled and `xp_cmdshell` was activated. This allowed execution of system commands directly through SQL queries.

Using `xp_cmdshell`, a PowerShell command was executed to download a Netcat binary (`nc64.exe`) from the attacker-controlled HTTP server onto the target system. A Netcat listener was started on the attacker machine, and the uploaded binary was used to initiate a reverse shell connection back to the attacker.

This provided interactive command execution as the MSSQL service account (`sql_svc`).

---

## Privilege Escalation

After obtaining a foothold on the system, local enumeration was performed. A privilege escalation enumeration tool (`winPEAS`) was uploaded to the target machine using the same file transfer technique.

Execution of `winPEAS` revealed that sensitive credentials had been previously entered into PowerShell commands. Investigation of the PowerShell history file (`ConsoleHost_history.txt`) uncovered plaintext Administrator credentials.

Using the recovered credentials, the attacker authenticated as Administrator via remote execution tools, achieving full system compromise.

---

## Access Gained

- **Initial access:** Reverse shell as `ARCHETYPE\sql_svc`  
- **Final access:** Administrator-level access to the target system  

---

## Mitigation / Defense

This attack could have been prevented through the following controls:

- Restricting anonymous access to SMB shares  
- Avoiding plaintext credential storage in configuration files  
- Enforcing least privilege for database service accounts  
- Keeping `xp_cmdshell` disabled  
- Monitoring SQL Server for configuration changes  
- Clearing or securing PowerShell command history  
- Implementing proper credential rotation policies  

---

## Lessons Learned

- Exposed SMB shares are high-risk attack vectors  
- Configuration files often contain critical credentials  
- MSSQL misconfigurations can lead to full system compromise  
- `xp_cmdshell` effectively grants OS-level command execution  
- Reverse shells are reliable footholds on Windows systems  
- Privilege escalation often comes from poor credential hygiene  
- Enumeration after initial access is critical  
