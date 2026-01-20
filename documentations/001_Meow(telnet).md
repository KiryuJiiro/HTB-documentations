**Overview**

Box: Meow

OS: Linux

Difficulty: Very easy

Focus: Telnet

The Meow machine demonstrated the risks of exposing legacy services such as Telnet with default credentials, allowing attackers to gain immediate unauthorized access.

**Background Study**

Telnet is a legacy remote access protocol that sends credentials in plaintext. Modern systems should use SSH instead, as it provides encrypted communication and stronger authentication mechanisms.

**Attack Approach**

- **Starting access level:** Unauthenticated external attacker
- **Expected attack surfaces:** Exposed network services

As an unauthenticated attacker, exposed network services were the primary attack surface. Legacy services like Telnet were used due to their lack of encryption and frequent misconfigurations involving weak or default credentials.

**Key Findings**

- Telnet service exposed to the network allowing remote login
- Misconfigured authentication indicating use of default or missing credentials on a legacy service

**Exploitation**

- **Vulnerability type:**  
    Insecure service configuration / Default credentials
- **Root cause:**  
    The Telnet service was exposed with root login enabled and no password configured, allowing unauthenticated users to authenticate directly without any credential validation.
- **Access gained:**  
    Full administrative (root) shell access

**Mitigation / Defense**

This issue could have been prevented by disabling legacy services such as Telnet, enforcing strong authentication, and restricting remote root login. Secure alternatives like SSH with key‑based authentication should be used instead.

**Lessons Learned**

- Legacy services remain a critical attack surface when left exposed
- Proper authentication controls are fundamental to system security
- Use of newer services like SSH is always recommended.