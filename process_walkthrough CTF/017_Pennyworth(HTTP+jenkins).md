# Pennyworth (HTB) Walkthrough

## 1. Reconnaissance
An initial full TCP port scan was performed against the target system to identify all exposed network services.


nmap -sS -sV -Pn -p- <target-ip>
Scan options explanation:
-sS performs a TCP SYN scan to identify open ports

-sV enables service and version detection

-Pn disables host discovery and assumes the host is online

-p- scans all 65535 TCP ports

Open ports discovered:
80 – HTTP

No additional services were exposed. Since service and version detection was enabled during the initial scan, no further targeted scans were required.

## 2. Web Enumeration
Port 80 was identified as an HTTP service. The web application was accessed using a browser.

    http://<target-ip>

The page revealed a Jenkins service running over HTTP. Jenkins is a continuous integration server that provides administrative functionality through a web interface.

## 3. Service Identification
The HTTP service was identified as Jenkins based on:

Page layout and UI elements

Jenkins dashboard structure

Presence of a login interface

Jenkins services are frequently targeted due to common misconfigurations and weak credential usage.

## 4. Authentication Testing
The Jenkins interface required authentication. Manual testing of common default credentials was performed based on publicly documented Jenkins defaults.

Successful authentication was achieved using:

    Username: root
    Password: password1

No security mechanisms such as account lockout, rate limiting, or CAPTCHA were present, indicating weak authentication controls.

## 5. Administrative Access
Successful login granted access to the Jenkins administrative dashboard.
From this interface, the authenticated user could:

Create and configure Jenkins jobs

Execute arbitrary shell commands via build steps

This confirmed the ability to achieve remote command execution through the Jenkins interface.

## 6. Command Execution via Jenkins
A new Jenkins job was created and configured to execute shell commands.

A reverse shell payload was added to the job configuration, instructing the target system to initiate a connection back to the attacker.

## 7. Reverse Shell Setup
A Netcat listener was started on the attacker machine to receive the reverse shell connection.

    nc -nlvp <listening-port>

Netcat options explanation:
-n disables DNS resolution

-l enables listening mode

-v enables verbose output

-p specifies the listening port

Upon executing the Jenkins job, a reverse shell connection was received.

## 8. Shell Stabilization
The initial reverse shell was non-interactive. A pseudo-terminal was spawned to improve usability.

    python3 -c 'import pty; pty.spawn("/bin/bash")'

Terminal settings were then adjusted:

    stty raw -echo -fg

The terminal environment variable was set to enable full terminal capabilities:

    export TERM=xterm

This resulted in a fully interactive shell session.

## 9. Access Confirmation
An interactive shell was successfully obtained on the target system.
This confirmed remote command execution and system access through Jenkins.

## 10. Exploitation Outcome
The system was compromised by exploiting a misconfigured Jenkins service.

The attack relied on:

Exposed Jenkins web interface

Weak default credentials

Lack of authentication hardening

Ability to execute arbitrary commands via Jenkins jobs

This resulted in remote shell access without requiring authentication bypass exploits, kernel vulnerabilities, or privilege escalation techniques.