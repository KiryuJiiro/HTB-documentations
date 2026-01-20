# Synced (HTB) Walkthrough

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

The scan revealed **TCP port 873** open, indicating the presence of an **rsync** service.

---

## 2. Service Identification

The open port **873** was identified as an **rsync daemon** service.

Rsync typically operates on this port when running in daemon mode and may expose shared directories (modules) if improperly configured.

---

## 3. rsync Enumeration

An anonymous connection attempt was made to the rsync service to enumerate available modules:

```bash
rsync rsync://<target-ip>/
```

The command successfully returned a list of accessible directories, confirming that **anonymous access** was enabled on the rsync daemon.

---

## 4. Directory Inspection

Each exposed rsync module was reviewed to identify potentially sensitive files.

One of the directories contained files that were accessible **without authentication**.

---

## 5. Data Retrieval

The contents of the identified directory were downloaded locally using rsync:

```bash
rsync rsync://<target-ip>/<directory-name> ./
```

The retrieved files were then examined on the local system.

---

## 6. Flag Discovery

Upon reviewing the downloaded files, the **flag** was located and read using standard file viewing commands.

---

## 7. Exploitation Outcome

The system was compromised due to a **misconfigured rsync daemon** that allowed anonymous access.

An unauthenticated attacker was able to enumerate shared directories and retrieve sensitive files, including the flag, without any authentication or advanced exploitation techniques.
