# Appointment (HTB) Walkthrough

## 1. Reconnaissance

An initial TCP port scan was performed against the target system to identify exposed network services.

```bash
nmap -sV -Pn -p- -sS <target-ip>
```

**Scan options explanation:**

* `-sV` enables service and version detection.
* `-Pn` disables host discovery and assumes the host is online.
* `-p-` scans all **65,535 TCP ports**.
* `-sS` performs a TCP SYN scan to identify open ports.

The scan revealed **TCP port 80** open, indicating the presence of an **HTTP web service**.

---

## 2. Service Identification

The open port **80** was identified as an **HTTP service** hosted by an **Apache web server**.

Since web applications commonly expose authentication mechanisms, further enumeration of the web service was required.

---

## 3. Web Enumeration

Directory enumeration was performed to identify accessible files and application entry points on the web server.

```bash
gobuster dir -u <target-url> -w <wordlist-path> -x php
```

**Command options explanation:**

* `dir` specifies directory enumeration mode.
* `-u` defines the target URL.
* `-w` specifies the wordlist used for enumeration.
* `-x php` appends the `.php` extension to each wordlist entry.

The enumeration identified **index.php** with an HTTP **200 OK** response, confirming it as a valid application page.

---

## 4. Application Access

Navigating to the web service root (`http://<target-ip>` or `http://<target-ip>:80`) automatically loaded **index.php**, presenting a **login page**.

This page served as the primary interaction point for the application.

---

## 5. SQL Injection Testing

The login form was tested for **SQL injection** by supplying crafted input into the username field.

The following payload was used:

```sql
admin'#
```

This payload terminates the SQL string and comments out the remainder of the query, including the password verification logic.

---

## 6. Authentication Bypass

The injected input altered the backend SQL query, effectively removing the password check while still querying for an existing user.

Since the **admin** user existed in the database, the application authenticated the session **without requiring valid credentials**.

---

## 7. Flag Discovery

Upon successful authentication, access to the protected page was granted.

The **flag** was displayed on this page and retrieved successfully.

---

## 8. Exploitation Outcome

The system was compromised due to **improper handling of user input** within an SQL query.

An unauthenticated attacker was able to bypass authentication entirely by exploiting an **SQL injection vulnerability**, resulting in unauthorized access and disclosure of sensitive information.
