# Explosion (HTB) Walkthrough

## 1. Reconnaissance

An initial **full TCP port scan** was performed against the target system to identify exposed network services.

```bash
nmap -sS -Pn -sV -p- <target-ip>
```

**Scan options explanation:**

* `-sS` performs a TCP SYN scan to identify open ports.
* `-Pn` disables host discovery and assumes the host is alive.
* `-sV` enables service and version detection.
* `-p-` scans all **65,535 TCP ports**.

The scan revealed that **TCP port 3389** was open.

---

## 2. Service Enumeration

After identifying an open port, a targeted scan was conducted to enumerate the service running on TCP port 3389.

```bash
nmap -sS -sV -v -Pn -sC -p 3389 <target-ip>
```

**Additional scan options explanation:**

* `-sC` runs Nmap default scripts for service enumeration.
* `-v` enables verbose output.

The scan identified the service as **ms-wbt-server**, corresponding to **Microsoft Terminal Services (RDP)**, confirming that the target was a Windows system with **Remote Desktop Protocol enabled**.

---

## 3. RDP Access Attempt

Since RDP was exposed, a remote desktop connection attempt was made using the **FreeRDP** client:

```bash
xfreerdp /v:<target-ip> /u:Administrator
```

**Command options explanation:**

* `/v:<target-ip>` specifies the target host.
* `/u:Administrator` specifies the Windows user account.

The connection prompted for a password. A **blank password** was submitted, and authentication succeeded.

This resulted in direct remote desktop access with full **graphical user interface (GUI)** control, confirming that the **Administrator account did not enforce password-based authentication**.

---

## 4. Flag Retrieval

Once logged into the system, the flag was found directly on the **Administrator desktop**.

The file was opened using **Notepad**, and the flag value was retrieved successfully.

---

## 5. Exploitation Outcome

The misconfigured RDP service allowed **unauthenticated remote access** to the system.

No exploitation techniques or privilege escalation were required to gain **full administrative access** and retrieve the flag.
