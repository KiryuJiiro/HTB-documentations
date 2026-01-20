# Preignition (HTB) Walkthrough

## 1. Reconnaissance

An initial TCP port scan was performed against the target system to identify exposed network services.

```bash
nmap -sS -Pn -sV -p- <target-ip>
```

**Scan options explanation:**

* `-sS` performs a TCP SYN scan to identify open ports.
* `-Pn` disables host discovery and assumes the host is online.
* `-sV` enables service and version detection.
* `-p-` scans all **65,535 TCP ports**.

The scan revealed that **TCP port 80** was open on the target system.

---

## 2. Service Enumeration

After identifying the open port, a targeted scan was conducted to enumerate the service running on TCP port 80.

```bash
nmap -sS -sV -v -Pn -p 80 <target-ip>
```

**Scan options explanation:**

* `-sV` enables service and version detection.
* `-v` enables verbose output.

The scan identified the web server as **nginx**, confirming that the target was hosting an **HTTP service**.

---

## 3. Web Enumeration (Directory Brute Forcing)

With an HTTP service identified, directory enumeration was performed to discover hidden files and directories.

```bash
gobuster dir -u http://<target-ip> -w <wordlist> -x php
```

**Command options explanation:**

* `dir` specifies directory brute forcing mode.
* `-u` specifies the target URL.
* `-w` specifies the wordlist used for enumeration.
* `-x php` instructs Gobuster to test PHP file extensions.

The scan revealed the presence of **admin.php** with an HTTP status code **200**, indicating that the page was accessible.

---

## 4. Authentication Bypass

The discovered administrative page was accessed directly through the browser:

```
http://<target-ip>/admin.php
```

The page presented a login interface. Default credentials were tested, and authentication succeeded using the following values:

* **Username:** `admin`
* **Password:** `admin`

This confirmed that the application was using **default credentials** without additional security controls.

---

## 5. Flag Retrieval

Upon successful authentication, access to the administrative interface was granted.

The flag was displayed within the application and retrieved successfully.

---

## 6. Exploitation Outcome

The system was compromised due to **weak authentication controls**.

No advanced exploitation techniques or privilege escalation were required. The presence of default credentials on a publicly accessible administrative interface resulted in **immediate unauthorized access**.
