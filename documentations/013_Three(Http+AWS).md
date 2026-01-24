# Three (HTB) Walkthrough

## Overview
**Box:** Three  
**OS:** Linux  
**Difficulty:** Very Easy  
**Focus:** Virtual Host Discovery → S3-Compatible Storage Misconfiguration → Arbitrary File Upload → Remote Code Execution  

The Three machine demonstrates how insecure cloud storage configurations can lead directly to remote code execution. By identifying a hidden virtual host running an S3-compatible storage service with overly permissive access controls, an attacker can upload executable web content and achieve command execution through the web server. This box highlights how cloud misconfigurations often eliminate the need for traditional exploitation techniques.

---

## Background Study

### Cloud Computing
Cloud computing refers to delivering computing services—such as storage, servers, and applications—over the network instead of hosting them locally. Rather than managing physical infrastructure, organizations rent services from providers or deploy cloud-like services themselves. Security responsibility often shifts to configuration rather than software flaws, making misconfigurations a common attack vector.

---

### Amazon Web Services (AWS)
Amazon Web Services (AWS) is Amazon’s cloud platform offering a wide range of services, including compute, storage, databases, and networking. AWS itself is an ecosystem; individual services within AWS provide specific functionality. In this box, the behavior of one AWS service (S3) is replicated without using Amazon’s infrastructure.

---

### S3 (Simple Storage Service)
S3 is an object storage service within AWS designed to store and retrieve files over HTTP using an API. Data is organized into **buckets**, which contain **objects** (files). Access to buckets is controlled through permissions that define whether users can list, read, write, or delete objects.

Key properties of S3:
- Uses HTTP-based APIs rather than traditional file systems
- Buckets act as top-level containers
- Objects are accessed directly by name
- Permissions are critical to security

---

### S3-Compatible Storage
S3-compatible storage refers to third-party or self-hosted services that implement the same API as AWS S3. Tools such as the AWS CLI can interact with these services by specifying a custom endpoint. From an attacker’s perspective, exploitation techniques are identical to those used against real AWS S3.

---

### Cloud Storage Misconfiguration
A common cloud security failure occurs when storage services are configured with excessive permissions, such as public listing or write access. If such storage is used to host web content, attackers may upload executable files and gain remote code execution without exploiting a traditional vulnerability.

---

## Attack Approach

- **Starting access level:** Unauthenticated external attacker  
- **Expected attack surfaces:**
  - HTTP web application (port 80)

Initial reconnaissance focused on identifying exposed services and understanding how the web application was hosted. Enumeration revealed that the application relied on virtual hosting, suggesting the presence of additional hidden services.

---

## Key Findings

- HTTP service exposed on TCP port 80  
- Website referenced a domain indicating name-based virtual hosting  
- Virtual host enumeration revealed an additional subdomain  
- Subdomain hosted an S3-compatible storage service  
- Storage service allowed unauthenticated bucket listing and file uploads  
- Uploaded files were served and executed by the web server  

---

## Exploitation

### Vulnerability Type
Cloud Storage Misconfiguration Leading to Arbitrary File Upload and Remote Code Execution  

---

### Root Cause
An S3-compatible storage service was exposed through a virtual host with no authentication or access restrictions. The storage bucket allowed unauthenticated users to upload files. Because the bucket was used to host web content, uploaded PHP files were executed by the backend server, resulting in remote code execution.

---

### Attack Execution
Initial port scanning identified an HTTP service on port 80. Visiting the site revealed a domain name, indicating the use of name-based virtual hosting. Virtual host enumeration uncovered an additional subdomain hosting an S3-compatible storage service.

Using the AWS CLI with a custom endpoint, the attacker enumerated available storage buckets and identified one with public write permissions. A PHP web shell was uploaded to the bucket. Accessing the uploaded file through the browser caused the server to execute the PHP code, granting command execution as the web server user.

---

### Access Gained
Remote code execution was achieved through the web server, allowing command execution on the underlying Linux system and retrieval of system flags without requiring authentication or SSH access.

---

## Mitigation / Defense

This attack could have been prevented through proper cloud security controls:

- Restricting public access to storage buckets  
- Enforcing authentication and authorization for object uploads  
- Disabling execution of server-side scripts from object storage  
- Separating static file storage from application execution paths  
- Regularly auditing cloud service permissions  
- Applying the principle of least privilege to cloud resources  

Cloud services should never be assumed secure by default; security depends entirely on configuration.

---

## Lessons Learned

- Cloud misconfigurations can directly lead to full system compromise  
- Virtual hosts may expose hidden services not visible through directory enumeration  
- S3-compatible services behave identically to real AWS S3 from an attacker’s perspective  
- Arbitrary file upload is often more dangerous than traditional vulnerabilities  
- Remote code execution does not always require authentication or exploits  
- Cloud security failures frequently replace classic exploitation techniques  
