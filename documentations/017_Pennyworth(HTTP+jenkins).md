# Pennyworth Walkthrough Documentation

## Overview
**Box:** Pennyworth  
**OS:** Linux  
**Difficulty:** Easy  
**Focus:** Web Enumeration → Jenkins Misconfiguration → Default Credentials → Remote Code Execution → Reverse Shell

The Pennyworth machine demonstrates how misconfigured CI/CD services and weak authentication can lead to full system compromise. By enumerating exposed web services, identifying an insecure Jenkins instance, and exploiting default credentials, an attacker can execute arbitrary commands and obtain a reverse shell. This box highlights the risks of exposed automation services, lack of credential hardening, and insufficient access control in web-based admin tools.

---

## Background Study

### Web Enumeration
Web enumeration involves identifying exposed services and applications running on a target system. Services like Jenkins often expose administrative functionality over HTTP and are frequently misconfigured or left protected by default credentials.

### Jenkins
Jenkins is a continuous integration and automation server that allows users to run jobs, scripts, and pipelines. If improperly secured, Jenkins can allow authenticated users to execute arbitrary system commands, effectively resulting in remote code execution.

### Weak Authentication
Weak authentication includes default credentials, predictable passwords, and lack of brute-force protection. Administrative services using such credentials can be trivially compromised.

### Reverse Shells
A reverse shell allows a compromised system to initiate a connection back to the attacker, providing interactive command execution. Netcat is commonly used as a listener to receive such shells.

---

## Attack Approach

- **Starting access level:** Unauthenticated external attacker  
- **Expected attack surfaces:**
  - HTTP service (port 80)
  - Jenkins web interface

The attack focused on discovering and abusing an exposed Jenkins service to gain remote command execution.

---

## Key Findings

- HTTP service exposed on port 80
- Web service hosted a Jenkins instance
- Jenkins login panel publicly accessible
- Default Jenkins credentials were valid
- Jenkins allowed execution of arbitrary shell commands
- No access restrictions or hardening applied to Jenkins
- Reverse shell successfully established via Netcat

---

## Exploitation

### Vulnerability Type
Misconfigured Jenkins Service → Weak Authentication → Remote Code Execution → Reverse Shell

### Root Cause
The Jenkins instance was publicly accessible and protected using default credentials. Once authenticated, Jenkins allowed execution of arbitrary shell commands without sandboxing or role-based access restrictions.

---

## Attack Execution

Initial reconnaissance using Nmap identified port 80 as the only exposed service. Accessing the web application revealed a Jenkins service running over HTTP.

Manual testing of common default Jenkins credentials led to successful authentication using known default values. After gaining access to the Jenkins dashboard, a new build/job was configured to execute system shell commands.

A reverse shell payload was injected into the Jenkins job configuration, which executed a Netcat-based callback to the attacker’s machine.

A Netcat listener was set up on the attacker system to receive the incoming connection. Upon job execution, the reverse shell was established, granting command execution on the target system.

To stabilize the shell, a Python pseudo-terminal was spawned using:

    python3 -c 'import pty; pty.spawn("/bin/bash")'


Terminal settings were corrected using `stty raw -echo` and job control was restored with `fg`. The terminal environment variable was set to enable proper terminal behavior.

---

## Access Gained

- Remote shell access to the target system
- Interactive command execution via stabilized reverse shell
- User-level access obtained through Jenkins command execution

---

## Mitigation / Defense

This attack could have been prevented through the following controls:

- Disabling or restricting public access to Jenkins
- Enforcing strong, non-default credentials
- Implementing role-based access control within Jenkins
- Running Jenkins with minimal system privileges
- Restricting command execution capabilities
- Monitoring Jenkins job creation and execution activity
- Binding administrative services to internal interfaces only

---

## Lessons Learned

- Exposed CI/CD tools are high-risk attack surfaces
- Default credentials remain a critical security failure
- Jenkins can directly lead to remote code execution if misconfigured
- Reverse shells via Jenkins are trivial once authenticated
- Shell stabilization techniques significantly improve post-exploitation control
- Even “internal” admin tools must be treated as internet-facing threats
