**Overview**

Box: Fawn  
OS: Linux  
Difficulty: Very easy  
Focus: FTP

The Fawn machine showed how dangerous it is to leave FTP open for anonymous login. Basically, anyone can connect without credentials and get access to files because the server was left completely open

**Background Study**

FTP or File Transfer Protocol is made for moving files between computers. It does not give you a system shell or let you run commands on the OS. If anonymous login is allowed it becomes easy for anyone to download sensitive files. Modern systems usually restrict FTP access, disable anonymous accounts or use secure alternatives like SFTP

**Attack Approach**

• **Starting access level:** Unauthenticated external attacker

• **Expected attack surfaces:** Exposed network services

As an unauthenticated attacker, exposed network services were identified as the primary attack surface. The FTP service was targeted due to its exposure to the network and the presence of anonymous authentication, which allowed access without valid credentials

**Key Findings**

- FTP was open on port 21
- Anonymous login was allowed
- The service was identified as vsftpd 3.0.3 using version detection

**Exploitation**

- **Vulnerability type:**  
    Insecure service configuration allowing anonymous FTP access
- **Root cause:**  
    The FTP service was exposed to the network with anonymous authentication enabled, permitting unauthenticated users to connect and access files without valid credentials
- **Exploitation details:**  
    An FTP connection was established using the anonymous account with no password required. File enumeration revealed the presence of the flag, which was downloaded and accessed locally
- **Access gained:**  
    Unauthorized file-level access through the FTP service with no system or shell access obtained

**Mitigation / Defense**

- Disable anonymous FTP authentication on all exposed services
- Enforce proper authentication and access control for file transfer services
- Use secure alternatives such as SFTP or FTPS instead of plain FTP
- Regularly audit exposed services to identify and remove insecure configurations

**Lessons Learned**

- Anonymous authentication on exposed services poses a significant security risk
- FTP provides limited access but can still lead to sensitive data exposure
- Service misconfigurations are common attack vectors even in simple environments
- Using secure, modern protocols and proper authentication is essential for system security