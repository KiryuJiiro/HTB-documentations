# Dancing (HTB) Walkthrough

## 1. Reconnaissance

An initial port scan was performed on the target to identify exposed services.

```bash
nmap -sS -sV -F -Pn <target-ip>
```

**Scan options explanation:**

* `-F` performs a fast scan of the top 100 common TCP ports.
* `-Pn` disables host discovery and assumes the host is alive.
* `-sV` identifies service versions.
* `-sS` performs a TCP SYN scan without completing the full handshake.

The scan revealed multiple open ports, including **TCP port 445**.

To further enumerate this service, a targeted scan was performed:

```bash
nmap -sV -Pn -p 445 <target-ip>
```

This confirmed that port **445** was open and running the **SMB service (microsoft-ds)**.

---

## 2. Service Enumeration

Since SMB was identified on port 445, enumeration of available shared resources was performed using:

```bash
smbclient -L <target-ip>
```

The SMB service prompted for authentication; however, submitting a **blank password** successfully authenticated, indicating that **anonymous access** was enabled.

This allowed enumeration of available shared folders on the target system.

---

## 3. Authentication

The SMB service allowed access without valid credentials using **null authentication**:

* **Username:** `anonymous` / `guest`
* **Password:** *(empty)*

Authentication succeeded, confirming improper access control on the SMB service.

Attempts were made to access each enumerated share using:

```bash
smbclient //<target-ip>/<share_name>
```

Some shares denied access, while at least one share allowed access using anonymous authentication.

---

## 4. Exploitation Outcome

Successful anonymous access to an SMB share resulted in **unauthorized access to files** stored within the shared directory, leading to **information disclosure**.

---

## 5. Post-Exploitation

After connecting to the accessible SMB share, the file system was navigated using standard SMB commands:

* `ls` to enumerate files
* `cd` to navigate directories

Files were transferred from the target system to the attacker machine using the `get` command. Once downloaded locally, the files were inspected and the **flag was obtained**.
