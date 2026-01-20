# Fawn (HTB) Walkthrough

## 1. Reconnaissance

An initial port scan was performed on the target IP using **Nmap** to identify open services.

```bash
nmap -sV -sS -O -F -Pn <target-ip>
```

**Scan options explanation:**

* `-sV` detects the version of the service running on open ports.
* `-sS` performs a TCP SYN scan without completing the full TCP handshake.
* `-O` attempts to identify the operating system of the target.
* `-F` limits the scan to the top 100 most common ports.
* `-Pn` disables host discovery and assumes the host is active.

The scan revealed that **port 21/tcp** was open, running the **FTP service (vsftpd 3.0.3)**.

---

## 2. Service Enumeration

Since FTP was identified on port 21, a direct connection attempt was made:

```bash
ftp <target-ip>
```

---

## 3. Authentication

Upon connecting, the FTP service prompted for login credentials. The following credentials were tested:

* **Username:** `anonymous`
* **Password:** *(blank)*

Authentication succeeded, granting immediate access to the FTP service.

---

## 4. Exploitation Outcome

Successful authentication resulted in **unauthorized access** to the FTP service, allowing file listing and file transfer operations on the target system.

---

## 5. Post-Exploitation

The files on the system were enumerated using the `ls` command within the FTP session.

Since direct command execution (e.g., `cat`) is not supported through FTP, the `get` command was used to download the flag file from the target machine to the attacker machine. After transferring the file locally, the flag was successfully captured.
