# Redeemer (HTB) Walkthrough

## 1. Reconnaissance

An initial **full TCP port scan** was performed on the target to identify exposed services.

```bash
nmap -p- -Pn <target-ip>
```

**Scan options explanation:**

* `-p-` scans all **65,535 TCP ports**.
* `-Pn` disables host discovery and assumes the host is alive.

This scan revealed that **TCP port 6379** was open.

To further enumerate the discovered port and identify the running service and version, a targeted scan was performed:

```bash
nmap -p 6379 -sV <target-ip>
```

**Scan options explanation:**

* `-p 6379` targets the Redis service port specifically.
* `-sV` performs service and version detection.

The scan confirmed that the service running on port **6379** was **Redis**.

---

## 2. Service Enumeration

Redis is an in-memory key–value database commonly used for caching and fast data access.

Since Redis was exposed externally, a direct connection attempt was made using the Redis command-line interface:

```bash
redis-cli -h <target-ip>
```

The connection succeeded **without requiring authentication**, indicating an insecure Redis configuration.

---

## 3. Redis Enumeration

Once connected, information about the Redis instance was gathered using:

```bash
INFO
```

This command revealed configuration details and available databases.

The default Redis database was then selected:

```bash
SELECT 0
```

---

## 4. Data Enumeration

All keys stored within the selected database were listed:

```bash
KEYS *
```

The returned keys were inspected, and relevant data was retrieved using:

```bash
GET <key_name>
```

One of the retrieved values contained the **flag**.

---

## 5. Exploitation Outcome

The misconfigured Redis service allowed **unauthenticated access** to sensitive in-memory data.

The flag was obtained without requiring authentication or privilege escalation.
