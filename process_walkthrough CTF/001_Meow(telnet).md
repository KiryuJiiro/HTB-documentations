# Meow (HTB) Walkthrough

## 1. Reconnaissance

An initial port scan was performed on the target to identify exposed services.

```bash
nmap -F -Pn <target-ip>
```

**Scan options explanation:**

* `-F` performs a fast scan of the top 100 common TCP ports.
* `-Pn` disables host discovery and assumes the host is alive, which is useful when ICMP is blocked.

The scan revealed that **TCP port 23** was open and running the **Telnet** service.

---

## 2. Service Enumeration

Since Telnet was identified on port 23, a direct connection attempt was made:

```bash
telnet <target-ip>
```

Telnet uses TCP port 23 by default, so no explicit port specification was required.

---

## 3. Authentication

Upon connecting, the Telnet service prompted for login credentials. The following default credentials were tested:

* **Username:** `root`
* **Password:** *(empty)*

Authentication succeeded, granting immediate access to the system.

---

## 4. Exploitation Outcome

Successful authentication resulted in a **root shell**, providing full administrative access to the target system.

No privilege escalation was required, as the service was misconfigured to allow direct root login with default or missing credentials.

---

## 5. Post-Exploitation

The file system was enumerated using standard Linux commands:

```bash
ls
```

The flag file was identified and read:

```bash
cat <flag_file>
```
