# Mongod (HTB) Walkthrough

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

The scan revealed multiple open ports, including **TCP port 27017**, indicating the presence of a **MongoDB** service.

---

## 2. Service Identification

The open port **27017** was identified as a **MongoDB database service**, which is associated with the MongoDB server daemon (`mongod`).

MongoDB commonly runs on this port and, if misconfigured, may allow **unauthenticated access** to stored databases.

---

## 3. Database Enumeration

A direct connection attempt was made to the MongoDB service using the MongoDB client:

```bash
mongo <target-ip>
```

The connection succeeded **without requiring authentication**, confirming that the MongoDB service was publicly exposed with authentication disabled.

---

## 4. Database Discovery

Once connected, available databases were enumerated to identify potentially sensitive data:

```bash
show dbs
```

A list of accessible databases was returned, indicating **unrestricted read access** to the MongoDB instance.

---

## 5. Collection Enumeration

A target database was selected, and its collections were enumerated to inspect stored data:

```bash
use <database-name>
show collections
```

The collections contained application-related data, including sensitive information.

---

## 6. Data Extraction

Documents stored within the identified collection were queried to retrieve their contents:

```bash
db.<collection-name>.find()
```

The output revealed the **flag** stored within one of the documents.

---

## 7. Exploitation Outcome

The system was compromised due to a **misconfigured MongoDB service**.

Authentication was disabled, and the database was directly exposed to the internet, allowing an unauthenticated attacker to enumerate databases and extract sensitive data without resistance.

No privilege escalation or advanced exploitation techniques were required.
